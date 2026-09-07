# Business Code Guardrails

**让 AI 修改业务代码时，说明规则从哪来，而不是把“看起来合理”直接写进代码。**

[English](README.md) | [Skill 指令](SKILL.md) | [合成案例](examples/scenarios.md)

一个面向存量业务系统的轻量 Skill，适用于开发和代码评审。关注的具体问题是：
代码能运行、测试能通过，但业务规则未经确认就被 AI 改了。

## 适合谁

- 维护订单、支付、退款、分账、库存、资格判断等业务的开发者。
- 对接第三方状态、标识符、回调协议的开发者。
- 需要检查历史数据、权限和旧客户端影响的评审人。
- 需求已经明确，但仍需核对现有链路和副作用的维护者。

纯样式调整、通用编程问题不需要它。已有同等规则核查流程的团队，也不必重复安装。
它不是需求管理框架、静态分析器或安全边界，不能代替测试和人工 CR。

## 它要求 AI 做什么

1. 区分代码现状、目标需求、外部协议，避免把旧实现当成永远正确的业务规则。
2. 追踪本次变更相关的链路，不无边界地扫描或重构整个系统。
3. 给影响行为的关键判断标明依据。
4. 只追问影响业务结果的未知项，不因普通技术细节阻塞开发。
5. 检查相关历史影响，如实说明实际验证范围。

仓库不包含运行时依赖、自动执行脚本、钩子或遥测。代理使用的工具和权限由宿主环境决定。

## 一个例子

**需求：**“揽收前允许取消。”

**不充分的判断：**“订单状态小于已发货，所以可以取消。”

**期望的处理：**先查揽收的权威信号和取消入口，确认落库状态是否有大小顺序，
检查其他商品类型是否共用判断。若缺少第三方揽收状态映射，只暂停相关判断的实现，
继续其他明确的排查工作。

这是合成示例，不是已完成的效果对照实验。分账和第三方状态映射见[案例说明](examples/scenarios.md)。

## 安装到 Codex

首次安装到用户级目录：

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/289989142/business-code-guardrails.git \
  "$HOME/.agents/skills/business-code-guardrails"
```

使用前先检查文件。如果目标目录已经存在，不要覆盖；先核对已有版本。
避免同时安装多个同名 Skill，包括旧目录中已经存在的安装。

显式调用示例：

```text
使用 $business-code-guardrails CR 当前改动。
先核对业务规则来源和历史影响，不改代码、不编译。
```

团队使用时，也可以将 Skill 目录放到目标仓库的 `.agents/skills/` 下。
安装位置以宿主版本为准，参见 [Codex 官方 Skill 文档](https://learn.chatgpt.com/docs/build-skills)。
其他支持 `SKILL.md` 的代理可以尝试使用这些指令，但本项目尚未验证跨宿主的发现和执行行为。

## 怎么选择

| 当前问题 | 更适合 |
| --- | --- |
| 需求不清楚，要整理验收条件 | [ECC 的 intent-driven-development](https://github.com/affaan-m/ecc/blob/main/skills/intent-driven-development/SKILL.md) |
| 修改或 CR 存量业务代码，要追溯规则、核对兼容性 | 本 Skill |
| 已出现异常，要定位根因 | [Superpowers 的 systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md) |
| 想采用更完整的工程工作流 | [Addy Osmani 的 agent-skills](https://github.com/addyosmani/agent-skills) |

上述项目是相关方案，不是本项目依赖，也不代表相互背书。各自有重叠，按缺口选择即可。
不建议叠加冲突的指令；任何 Skill 都不应自行扩大编译、生产访问或发布权限。

## 验证与边界

提供[评估用例](evals/cases.json)和[对照方法](evals/README.md)，覆盖新旧需求冲突、
协议缺失、落库枚举、执行权限和过度追问。

这些是待执行的行为评估用例，**不是已经通过的行为测试**。
本项目不宣称错误降低比例、模型排名或全平台兼容。结构校验通过也不代表业务判断更准确。

## 贡献

欢迎用 Issue 或 PR 提交最小合成案例，说明用户请求、期望行为、实际决策及其依据。
不要提交公司源码、客户信息、凭据、内网地址或私人对话原文。
优先针对可复现的问题改进，而不是不断增加全局禁止项。

## 许可证

[MIT](LICENSE)。
