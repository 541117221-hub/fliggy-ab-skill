---
name: ab-experiment-report
description: 通用AB实验数据分析与报告生成。支持从fliggy_data_mcp查询实验数据、截图OCR识别、文本输入等多种数据源，自动计算转化率/L2O、绝对提升、相对提升，执行双样本比例Z检验，生成交互式HTML实验报告。适用于AB测试、实验效果评估、对照组实验组对比分析等场景。
---

# AB实验数据分析与报告生成

## 触发条件

当用户提到以下任一关键词时触发：AB实验、A/B测试、实验分析、实验报告、实验组/对照组、实验效果、显著性检验、转化率对比、实验数据、AB test。

---

## 执行流程

### 第一步：向用户确认实验参数

触发后**必须先询问**用户以下信息，缺一不可：

| 序号 | 必问信息 | 示例 |
|------|----------|------|
| 1 | 实验ID | 808 |
| 2 | 对照组分组ID | 3876, 3870 |
| 3 | 实验组分组ID | 3877, 3871 |
| 4 | 是否需要人群维度拆分 | 88VIP（`is_88_vip`）、诸葛人群等 |
| 5 | 观测指标口径 | 酒店列表页曝光UV → 详情页UV → 下单UV |
| 6 | 查询日期范围 | 2026-05-08 至 2026-05-13 |

> **人群拆分支持情况**：`hotel_decision`空间下，`is_88_vip`维度可用，返回Y/N/null三种值。查询时将`is_88_vip`加入`dimensions`数组即可一次性返回拆分数据。若其他人群维度不可用，再降级到ODPS或截图方式。

---

### 第二步：获取实验数据

**数据源优先级**：FDP查询 > 截图OCR > 文本输入 > CSV/Excel

#### 2.1 FDP查询（首选）

调用 `mcp__fliggy_data_mcp__fdp_data_olap_query`，param为JSON字符串。

**参数模板：**

```json
{
  "businessSpace": "hotel_decision",
  "moduleName": "实验分析",
  "joinType": "FULL",
  "dateRange": {"start": "2026-05-08", "end": "2026-05-13"},
  "dimensions": ["biz_date", "abtest_group_id", "is_88_vip"],
  "measures": ["passageway_pv_listing", "htl_listing_ipv_fromlisting", "reserve_uv_1d_all_zx_cal"],
  "filter": {
    "concat": "AND",
    "children": [
      {"key": "abtest_id", "operator": "=", "value": "808"},
      {"key": "abtest_group_id", "operator": "in", "value": "'3870','3871','3876','3877'"}
    ]
  },
  "limit": {"count": 1000, "start": 0},
  "extParams": {
    "isCalDayAvg": false,
    "sameTermRatioTypes": [],
    "isRemoveBizDateFilter": true,
    "skillVersion": "1.3.0",
    "query": "实验数据查询"
  }
}
```

**关键格式要求（已踩坑验证）：**

| 字段 | 要求 | 错误示例 | 正确示例 |
|------|------|----------|----------|
| `abtest_id` | 字符串 | `808`（整数） | `"808"` |
| `in`操作符value | 每个值加单引号 | `"3870,3871"` | `"'3870','3871'"` |
| `extParams` | `skillVersion`和`query`必填 | 省略 | 见模板 |

**可用指标（推荐组合）：**

| 指标code | 含义 | 漏斗位置 | 备注 |
|----------|------|----------|------|
| `htl_listing_pv_fromlisting` | 酒店listing页曝光UV | 漏斗起点 | **首选**，数据完整 |
| `passageway_pv_listing` | 酒店listing页通道引流曝光UV | 漏斗起点 | 备选，部分日期可能缺失 |
| `htl_listing_ipv_fromlisting` | 酒店listing页引导ipvuv | 漏斗中段 | 详情页UV |
| `reserve_uv_1d_all_zx_cal` | B2C线上四端国内酒店日历下单UV | 漏斗终点 | 下单UV |

**三指标联合查询**：将三个指标放入同一个`measures`数组，可一次性返回曝光、IPV、下单UV，无需分次查询再合并。

**数据完整性检查流程**：
1. 查询返回后检查每个指标每天的值，标记null值
2. 若某日期指标为null，调用`mcp__fliggy_data_mcp__query_fdp_log`获取traceId，分析SQL执行日志确认是底表无数据还是查询异常
3. 若确认底表缺失，寻找替代指标（如`htl_listing_pv_fromlisting`替代`passageway_pv_listing`），重新查询验证

**查询失败兜底：** 若FDP查询失败，调用 `fliggy-sql-writer` skill生成ODPS SQL。

#### 2.2 其他数据源

- **截图/图片**：Read工具读取 → 人工识别表格 → 结构化整理
- **文本输入**：请用户按日期分行提供：曝光UV、IPVUV、下单UV
- **CSV/Excel**：Read工具读取 → 解析结构化数据

---

### 第三步：数据分析

#### 3.1 漏斗指标计算

| 指标 | 公式 | 业务含义 |
|------|------|----------|
| L2D | IPVUV / 曝光UV | 列表页引导能力 |
| D2O | 下单UV / IPVUV | 详情页转化能力 |
| L2O | 下单UV / 曝光UV | 整体转化效率 |

对每一天和汇总分别计算：对照组L2D/L2O/D2O、实验组L2D/L2O/D2O。

#### 3.2 效果度量

- **绝对提升** = 实验组转化率 - 对照组转化率（单位：**pt**，百分点）
- **相对提升** = (实验组 - 对照组) / 对照组 × 100%

#### 3.3 统计显著性检验（双样本比例Z检验）

```python
import math

def norm_cdf(x):
    return 0.5 * (1 + math.erf(x / math.sqrt(2)))

def z_test(n_ctrl, x_ctrl, n_exp, x_exp):
    """n: UV, x: 下单UV"""
    p_ctrl = x_ctrl / n_ctrl
    p_exp = x_exp / n_exp
    p_pool = (x_ctrl + x_exp) / (n_ctrl + n_exp)
    se = math.sqrt(p_pool * (1 - p_pool) * (1/n_ctrl + 1/n_exp))
    z = (p_exp - p_ctrl) / se if se > 0 else 0
    p_value = 2 * (1 - norm_cdf(abs(z)))
    return z, p_value, p_exp - p_ctrl
```

| P值范围 | 显著性 |
|---------|--------|
| < 0.01 | 高度显著 |
| < 0.05 | 显著（决策依据） |
| < 0.10 | 边缘显著 |
| ≥ 0.10 | 不显著 |

#### 3.4 功效分析（未达显著时）

```python
# α=0.05, power=0.8
z_a, z_b = 1.96, 0.8416
n_needed = ((z_a + z_b)**2 * 2 * p_avg * (1 - p_avg)) / (delta**2)
```

---

### 第四步：生成HTML报告

#### 4.1 报告模块

1. **标题区**：实验名称、观测周期、指标口径
2. **KPI卡片**：各群体绝对提升（pt）+ 显著性状态标签
3. **核心结论**：3-5条要点，含显著性结论
4. **综合数据对比表**：对照组/实验组 L2O/L2D/D2O、绝对提升、Z值、P值
5. **趋势可视化**（Chart.js）：
   - 每日L2O对照vs实验折线图
   - 每日绝对提升柱状图
   - 漏斗分析时增加：每日L2D和D2O折线图
6. **分群体详细数据表**：逐日曝光UV、IPVUV、下单UV、L2D、D2O、L2O、绝对提升
7. **漏斗分析**（按需）：L2D和D2O对照vs实验对比表
8. **统计显著性检验**：汇总检验 + 逐日明细 + 功效分析
9. **分析与建议**：效果解读 + 后续建议

#### 4.2 视觉规范

| 元素 | 规范 |
|------|------|
| KPI卡片 | 渐变色背景，绝对提升为主数字（大号） |
| 显著性标签 | 绿色(p<0.05)、黄色(p<0.10)、灰色(不显著) |
| 表格 | 表头深蓝(#1a365d)，汇总行浅蓝(#ebf4ff) |
| 数值颜色 | 正值绿色(#22543d)，负值红色(#c53030) |

#### 4.3 口径标注（必须）

报告中必须注明：下单UV是否为多天去重、曝光UV口径名称、观测周期天数。

#### 4.4 报告结论必须与数据一致（关键！）

**严禁硬编码结论文字**。所有结论（显著性判断、效应量描述、建议）必须通过Python变量动态插入。数据更新后重新生成报告时，若发现结论文字与数据矛盾，必须同步更新。典型错误：数据已显著但文字仍写"未达显著"。

---

### 第五步：输出保存

1. 默认保存路径：`用户工作目录/outputs/实验{ID}-{名称}/`
2. 文件名：`{实验名称}实验效果分析报告.html`
3. 使用 `present_files` 展示给用户

---

## 常见陷阱与注意事项

### 陷阱1：Python f-string生成HTML时字符串截断

当使用`html = f'''...'''`包裹HTML时，若HTML中包含JavaScript数据注入如`data: ['''`或`''']`，三重引号会**提前结束**Python字符串，导致后半段变成普通字符串，`{{`无法被f-string转义为`{`，最终HTML中Chart.js出现`{{`语法错误，图表无法渲染。

**修复方案**：
- 方案A：改用`f"""..."""`包裹（JavaScript用单引号字符串）
- 方案B：将Chart.js数据部分拆分为独立变量，用`{chart_js}`插值
- 方案C：生成后用`sed`修复：`sed -i 's/{{/{/g; s/}}/}/g'`

### 陷阱2：数据更新后结论未同步更新

用户追加新数据后，显著性可能从"不显著"变为"显著"。报告中的文字结论、建议（如"继续观察"改为"可决策推全"）必须同步更新。**结论部分严禁写死**，应通过变量动态生成。

---

## 多维度实验处理

当实验含交叉维度（如88VIP × 实验/对照）：

1. **FDP人群维度支持**：`hotel_decision`空间下，在`dimensions`中加入`is_88_vip`即可一次性返回按88VIP拆分的每日数据，返回值为Y/N/null三种。需在分析时过滤null行。
2. 各维度子群体独立分析 + 全量汇总（样本加权）
3. 对比子群体效果差异，在结论中分析原因

---

## 术语与精度规范

| 项目 | 规范 |
|------|------|
| 单位 | 绝对提升用 **pt**（百分点），不用pp |
| 转化率术语 | 统一用 **L2O**（List to Order），不用CVR |
| P值 | 保留4位小数 |
| Z值 | 保留3位小数 |
| 绝对提升 | 日报保留2位小数，汇总保留4位小数 |
| 日报行数 | >10天时可折叠，默认展示汇总+图表 |
