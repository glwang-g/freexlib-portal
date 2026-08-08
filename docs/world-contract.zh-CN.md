# Freexlib 世界契约

**状态：** 持续演进文档 · **归属仓库：** `freexlib-portal`
**读者：** swarm-space、living-world、xshow 的开发者

## 为什么需要这份契约

三个 Freexlib 空间收敛到同一个想法：一个规则可观察、行动留下后果、历史可回放
并可编程的世界。三个仓库各自独立得到了同一套架构：

- swarm-space：`swarm-core -> swarm-runner -> WASM 适配器 -> Canvas`
- living-world：`world-core -> world-runner -> world-bot -> world-protocol -> app`
- xshow：`规则模块 -> world-engine 契约 -> Vue/canvas 可视化`

本契约是一份共享词汇表和分层映射。它不是共享代码库；每个世界保留自己的权威引擎。

## 规范术语

| 术语 | 含义 | 备注 |
| --- | --- | --- |
| World（世界） | 权威状态 + 改变它的规则；每个世界有且只有一个所有者 | swarm `Simulation`、living `World`、xshow `WorldState` |
| Entity（实体） | 世界中拥有稳定 id 的可识别对象 | 无人机、居民、电路元件、CPU 单元 |
| State（状态） | 世界/实体状态的可序列化投影 | `RenderSnapshot`、`WorldSnapshot`、`CpuSnapshot` |
| Tick | 世界的一次原子推进 | `resolve_tick`、runner tick、`stepTankBattle()` |
| Observation（观察） | 代理收到的受限视图 | `swarm-core::Observation`、`world-bot` |
| Intent / Action（意图/行动） | 代理提议要做的事 | `Intent`/`Decision`、`WorldAction`、`TankAction` |
| Rule（规则） | 由状态 + 行动确定性地推进一个 tick 的函数 | `resolve_tick`、`world-core` 规则、`evaluateCircuit()`、CPU 取指/译码/执行 |
| Event（事件） | 追加到世界历史的不可变结构化事实 | `WorldEvent`、`world.events.log`、CPU/战车 trace |
| Replay（回放） | 足以重建或解释一次运行的时序历史 | `MatchEvent` + replay、历史视角、`WorldReplay` |
| Snapshot（快照） | 给观看者的稳定序列化；观看者绝不修改权威 | `RenderSnapshot`、`/api/snapshot` |
| Scenario（场景） | 带初始世界的种子 + 目标/课程 | 比赛场景、世界种子/档案、课程 starter |
| Bot（代理） | 读取 `Observation`、产出 `Intent`，从不拥有世界 | `BaselineBot`、`world-bot`、`TankStrategy`、CPU 控制台 |
| Visualizer（表现层） | 渲染快照，从不拥有权威 | Canvas 客户端、`app/`、Vue 工作台 |

## 规范事件形态

```json
{
  "tick": 1,
  "actor": "drone-3",
  "action": "harvest",
  "facts": ["collected crystal at (4,7)"],
  "visible_to": ["amber"],
  "consequences": ["amber score +1"]
}
```

对齐方式：

- swarm-space：`WorldEvent` 已经携带结构化事实；`MatchEvent` 携带比赛级事件。
- living-world：`world.events.log` 条目；角色视角和历史视角是同一事件流的投影。
- xshow：`WorldReplay` 与 trace 记录（CPU log、战车事件、逻辑 trace）在出现共享
  runner 时应向这个形态收敛。

## 权威边界（始终成立）

1. 每个世界有且只有一个权威引擎。
2. Bot 和玩家只读 `Observation`/快照，只提交 `Intent`/行动。
3. UI 和可视化只读快照，绝不裁决规则。
4. 历史只追加且可回放。
5. 规则内时间确定（`TickBudget`/固定刻度），不依赖墙钟。

## 变更协议

- 新增或修改共享术语时，同步更新本文档（中英文版本一致）。
- 仓库重命名边界 crate/模块时，更新映射表。
- 跨仓库链接：swarm-space `AGENTS.md`、living-world `AGENTS.md` 和
  `docs/DEVELOPMENT_LOG.md`、xshow `docs/context-index.md` 和 `AGENTS.md`。

## 非目标

- 暂不共享代码库；只有两个世界真正需要同一套规则语义时才合并。
- 不统一 UI；Canvas、React/Next、Vue 保留各自界面。
- 不强制发布节奏；各仓库独立发布，只有门户和本契约是共享的。
