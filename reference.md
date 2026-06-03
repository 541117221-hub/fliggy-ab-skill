# AB实验报告 HTML 模板参考

## 完整HTML报告结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{实验名称}实验效果分析报告</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  /* 基础 */
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", sans-serif; background: #f0f4f8; color: #1a202c; line-height: 1.7; }
  .container { max-width: 1100px; margin: 0 auto; padding: 40px 24px; }

  /* 头部 */
  .header { text-align: center; margin-bottom: 48px; }
  .header h1 { font-size: 32px; font-weight: 700; color: #1a365d; margin-bottom: 8px; }
  .header .subtitle { font-size: 15px; color: #718096; }

  /* 卡片 */
  .card { background: #fff; border-radius: 12px; padding: 32px; margin-bottom: 28px; box-shadow: 0 1px 3px rgba(0,0,0,0.06); }
  .card h2 { font-size: 20px; font-weight: 600; color: #1a365d; margin-bottom: 16px; padding-bottom: 10px; border-bottom: 2px solid #e2e8f0; }
  .card h3 { font-size: 17px; font-weight: 600; color: #2d3748; margin: 20px 0 12px; }

  /* KPI */
  .kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 16px; margin-bottom: 28px; }
  .kpi-card { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); border-radius: 12px; padding: 24px; color: #fff; text-align: center; }
  .kpi-card.green { background: linear-gradient(135deg, #38b2ac 0%, #2f855a 100%); }
  .kpi-card.blue { background: linear-gradient(135deg, #4299e1 0%, #3182ce 100%); }
  .kpi-card .kpi-label { font-size: 13px; opacity: 0.9; margin-bottom: 6px; }
  .kpi-card .kpi-value { font-size: 32px; font-weight: 700; }
  .kpi-card .kpi-sub { font-size: 12px; opacity: 0.8; margin-top: 4px; }

  /* 表格 */
  .table-wrap { overflow-x: auto; margin: 16px 0; }
  table { width: 100%; border-collapse: collapse; font-size: 14px; }
  thead th { background: #1a365d; color: #fff; padding: 10px 12px; text-align: center; font-weight: 500; }
  tbody td { padding: 9px 12px; text-align: center; border-bottom: 1px solid #e2e8f0; }
  tbody tr:nth-child(even) { background: #f7fafc; }
  tbody tr.summary-row { background: #ebf4ff; font-weight: 600; }
  .positive { color: #22543d; font-weight: 600; }
  .negative { color: #c53030; font-weight: 600; }

  /* 图表 */
  .chart-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; margin: 20px 0; }
  .chart-box { background: #f7fafc; border-radius: 8px; padding: 16px; }
  @media (max-width: 768px) { .chart-grid { grid-template-columns: 1fr; } }

  /* 标签 */
  .tag { display: inline-block; padding: 3px 10px; border-radius: 20px; font-size: 12px; font-weight: 500; margin-right: 6px; }
  .tag-green { background: #c6f6d5; color: #22543d; }
  .tag-blue { background: #bee3f8; color: #2a4365; }
  .tag-yellow { background: #fefcbf; color: #744210; }

  /* 提示框 */
  .note { background: #fffbeb; border-left: 4px solid #ecc94b; padding: 14px 18px; border-radius: 0 8px 8px 0; margin-top: 20px; font-size: 14px; color: #744210; }
  .note-success { background: #f0fff4; border-left: 4px solid #38a169; color: #22543d; }
  .footer { text-align: center; margin-top: 40px; font-size: 13px; color: #a0aec0; }
</style>
</head>
<body>
<div class="container">
  <!-- 标题 -->
  <div class="header">
    <h1>{实验名称} 实验效果分析报告</h1>
    <p class="subtitle">实验周期：{起始日期} - {结束日期}（{N}天）| 观测指标：{指标口径}</p>
  </div>

  <!-- KPI卡片（每个维度一个） -->
  <div class="kpi-grid">
    <!-- 动态生成 -->
  </div>

  <!-- 核心结论 -->
  <div class="card">
    <h2>核心结论</h2>
    <ul>
      <!-- 动态生成 -->
    </ul>
  </div>

  <!-- 综合数据对比 -->
  <div class="card">
    <h2>综合数据对比</h2>
    <div class="table-wrap">
      <table><!-- 动态生成 --></table>
    </div>
  </div>

  <!-- 趋势可视化 -->
  <div class="card">
    <h2>趋势可视化</h2>
    <div class="chart-grid">
      <!-- 动态生成 Chart.js canvas -->
    </div>
  </div>

  <!-- 分群体详细数据 -->
  <div class="card">
    <h2>分群体详细数据</h2>
    <!-- 每个群体一个table -->
  </div>

  <!-- 统计显著性检验 -->
  <div class="card">
    <h2>统计显著性检验</h2>
    <!-- 汇总表 + 逐日明细 + 功效分析 -->
  </div>

  <!-- 分析与建议 -->
  <div class="card">
    <h2>分析与建议</h2>
    <!-- 效果解读 + 后续建议 -->
  </div>

  <div class="footer">
    <p>{实验名称}实验分析报告 | 数据周期 {日期范围} | 数据口径：{口径说明}</p>
  </div>
</div>

<script>
  // Chart.js 配置
  // 折线图选项
  const lineOpts = {
    responsive: true,
    plugins: { legend: { position: 'bottom' } },
    scales: { y: { ticks: { callback: v => v + '%' } } },
    elements: { point: { radius: 4 }, line: { tension: 0.3 } }
  };
  // 柱状图选项（绝对提升）
  const barOpts = {
    responsive: true,
    plugins: { legend: { display: false } },
    scales: { y: { ticks: { callback: v => v + 'pt' } } }
  };
</script>
</body>
</html>
```

## Python生成HTML报告注意事项

### 避免f-string三重引号截断

**错误写法**（`'''`在JS数据中被截断）：
```python
html = f'''
<script>
const data = [''' + ",".join(values) + '''];
</script>
'''
```

**正确写法A**（改用`"""`）：
```python
html = f"""
<script>
const data = [""" + ",".join(values) + """];
</script>
"""
```

**正确写法B**（独立变量拼接）：
```python
chart_data = ",".join(f"{d['ctrl_l2o']:.2f}" for d in daily_trend)
html = f'''
<script>
const ctrlData = [{chart_data}];
</script>
'''
```

### 结论文字必须动态生成

```python
def conclusion_text(p_value, lift_pt):
    if p_value < 0.05:
        return f"全量L2O已达显著（+{lift_pt:.2f}pt, P={p_value:.4f}），建议推全"
    elif p_value < 0.10:
        return f"全量L2O边缘显著（+{lift_pt:.2f}pt, P={p_value:.4f}），建议继续观察"
    else:
        return f"全量L2O未达显著（+{lift_pt:.2f}pt, P={p_value:.4f}），建议继续跑量"
```

---

## Z检验Python代码模板

```python
import math

def norm_cdf(x):
    """标准正态分布累积分布函数"""
    return 0.5 * (1 + math.erf(x / math.sqrt(2)))

def two_proportion_ztest(n_ctrl, x_ctrl, n_exp, x_exp):
    """
    双样本比例Z检验
    n_ctrl: 对照组样本量（UV）
    x_ctrl: 对照组成功数（下单UV）
    n_exp: 实验组样本量（UV）
    x_exp: 实验组成功数（下单UV）
    返回: (z值, p值, 绝对提升)
    """
    p_ctrl = x_ctrl / n_ctrl
    p_exp = x_exp / n_exp
    p_pool = (x_ctrl + x_exp) / (n_ctrl + n_exp)
    se = math.sqrt(p_pool * (1 - p_pool) * (1/n_ctrl + 1/n_exp))
    z = (p_exp - p_ctrl) / se if se > 0 else 0
    p_value = 2 * (1 - norm_cdf(abs(z)))
    return z, p_value, p_exp - p_ctrl

def sig_label(p):
    if p < 0.01: return "高度显著(p<0.01)"
    elif p < 0.05: return "显著(p<0.05)"
    elif p < 0.10: return "边缘显著(p<0.10)"
    else: return "不显著"

def power_analysis(n_ctrl, x_ctrl, n_exp, x_exp, days):
    """计算达到显著所需天数"""
    p1 = x_ctrl / n_ctrl
    p2 = x_exp / n_exp
    delta = abs(p2 - p1)
    if delta == 0: return None
    p_avg = (p1 + p2) / 2
    z_a, z_b = 1.96, 0.8416
    n_needed = ((z_a + z_b)**2 * 2 * p_avg * (1 - p_avg)) / (delta**2)
    daily_avg = (n_ctrl / days + n_exp / days) / 2
    days_needed = math.ceil(n_needed / daily_avg)
    return {
        "effect_size": delta * 100,  # pt
        "n_per_group": n_needed,
        "daily_uv": daily_avg,
        "days_needed": days_needed
    }
```

---

## 增量UV估算 + 推全外推 Python 模板（v8 标准）

```python
# 流量配比假设（默认值；用户可指定）
TRAFFIC_RATIO = 0.25       # 实验桶占大盘比例
ROLLOUT_MULT = 1 / TRAFFIC_RATIO  # 推全倍数（口径A=4）
NIGHTS_PER_UV = 1.5        # 间夜换算系数（酒店业务历史经验值）
N_DAYS = 14                # 实验观测天数

def estimate_incremental_uv(pv_ctrl, order_uv_ctrl, pv_exp, order_uv_exp, n_days):
    """
    增量下单UV估算（曝光对等假设·避免伪负值）
    返回三种口径 + 日均 + 推全估算 + 间夜估算
    """
    l2o_ctrl = order_uv_ctrl / pv_ctrl
    l2o_exp  = order_uv_exp / pv_exp
    delta_l2o = l2o_exp - l2o_ctrl

    # 三种口径
    inc_by_exp_pv  = delta_l2o * pv_exp                  # 旁证1：实验组PV
    inc_by_ctrl_pv = delta_l2o * pv_ctrl                 # 旁证2：对照组PV
    inc_by_avg_pv  = delta_l2o * (pv_ctrl + pv_exp) / 2  # 主推：双方平均PV

    # 日均增量
    inc_daily = inc_by_avg_pv / n_days

    # 推全后日均（口径A：vs 实验上线前）
    rollout_daily = inc_daily * ROLLOUT_MULT

    # 间夜换算（酒店业务）
    rollout_nights_daily = rollout_daily * NIGHTS_PER_UV

    return {
        "delta_l2o_pt": delta_l2o * 100,
        "inc_by_exp_pv": inc_by_exp_pv,
        "inc_by_ctrl_pv": inc_by_ctrl_pv,
        "inc_by_avg_pv": inc_by_avg_pv,    # 主推
        "inc_daily": inc_daily,
        "rollout_daily_a": rollout_daily,  # 口径A唯一
        "rollout_nights_daily": rollout_nights_daily,
    }
```

### 日均比率算数平均（按天平权）

```python
def daily_avg_rate(daily_records, num_field, den_field, n_days):
    """
    比率类指标的日均（算数平均，按天平权）
    与 sum(num)/sum(den) 累计口径区分使用
    """
    return sum(r[num_field] / r[den_field] for r in daily_records if r[den_field] > 0) / n_days

# 用法
ctrl_l2o_daily_avg = daily_avg_rate(ctrl_records, 'order_uv', 'pv', N_DAYS)
# 注意：第二章Z检验仍用累计口径
ctrl_l2o_cumulative = sum(r['order_uv'] for r in ctrl_records) / sum(r['pv'] for r in ctrl_records)
```

---

## 报告章节模板片段（v8 标准）

### 实验背景与策略 section（第0章）

```html
<div class="card">
  <h2>实验背景与策略</h2>
  <div class="kpi-grid" style="grid-template-columns: 1fr 1fr;">
    <div class="kpi-card blue">
      <div class="kpi-label">策略一</div>
      <div class="kpi-value" style="font-size: 18px;">{策略一名称}</div>
      <div class="kpi-sub">{适用人群} · {机制说明}</div>
    </div>
    <div class="kpi-card" style="background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);">
      <div class="kpi-label">策略二</div>
      <div class="kpi-value" style="font-size: 18px;">{策略二名称}</div>
      <div class="kpi-sub">{适用人群} · {机制说明}</div>
    </div>
  </div>
</div>
```

### 推全外推表格（第3章）

```html
<table>
  <thead>
    <tr>
      <th>群体</th>
      <th>当前桶日均增量UV</th>
      <th>推全日均增量UV<br>(vs 实验上线前)</th>
      <th>推全日均增量间夜<br>(× 1.5 间夜/UV)</th>
    </tr>
  </thead>
  <tbody><!-- 动态生成 --></tbody>
</table>
<div class="note">
  <strong>外推前提：</strong>实验流量占大盘 25%，推全倍数 ×4；对比基准为实验上线前；
  <strong>间夜估算：</strong>按 <strong>1.5 间夜 / 下单UV</strong>（酒店业务历史经验值）换算。
</div>
```

### 推全 KPI 双卡（第3章）

```html
<div class="kpi-grid">
  <div class="kpi-card blue">
    <div class="kpi-label">推全后日均增量UV</div>
    <div class="kpi-value">+{rollout_daily:,.0f}</div>
    <div class="kpi-sub">单/日 · vs 实验上线前</div>
  </div>
  <div class="kpi-card green">
    <div class="kpi-label">推全后日均增量间夜</div>
    <div class="kpi-value">+{rollout_nights:,.0f}</div>
    <div class="kpi-sub">间夜/日 · 1.5 间夜/UV</div>
  </div>
</div>
```
