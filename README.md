# ab-experiment-report

通用AB实验数据分析与报告生成技能，适用于QoderWork Agent。

## 功能

- 从 `fliggy_data_mcp` 查询实验数据（支持FDP三指标联合查询）
- 截图OCR识别、文本输入、CSV/Excel等多种数据源
- 自动计算漏斗指标（L2D/D2O/L2O）、绝对提升、相对提升
- 双样本比例Z检验（显著性检验）
- 功效分析（计算达到显著所需天数）
- 生成交互式HTML实验报告（含Chart.js图表）
- 支持88VIP等人群维度拆分分析

## 文件结构

```
ab-experiment-report/
├── SKILL.md       # 主技能文档（触发条件、执行流程、参数模板）
├── reference.md   # HTML模板 + Python代码参考
├── examples.md    # 使用示例（FDP查询、漏斗分析、多维度实验）
└── README.md      # 本文件
```

## 使用场景

- AB测试效果评估
- 对照组/实验组转化率对比
- 实验显著性检验与决策建议
- 多维度交叉实验分析（如88VIP × 实验/对照）

## 技术栈

- Python（双样本比例Z检验）
- HTML + Chart.js（交互式报告）
- FDP fliggy_data_mcp（数据查询）

## 版本历史

- v1.0: 基础AB实验分析功能
- v1.1: 增加FDP三指标联合查询、88VIP维度支持
- v1.2: 增加数据完整性检查、替代指标方案
- v1.3: 增加HTML报告生成陷阱防范（f-string截断、结论同步）

## License

MIT
