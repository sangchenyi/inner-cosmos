# 内宇宙完全体 — 四路并行执行方案 v1.0

> 制定日期：2026-07-17
> 依据：`goal-objective.md` §6 完成定义、`docs/goal/complete-product-acceptance.yml`（验收账本 G0–G9）、
> `docs/goal/loop-goal-directive.md`（操作者指令 v2）、四路独立核查（k8s 实测 / 构建实测 / 账本核查 / AI 核心源码审计，2026-07-17）。
> 用途：组员（最多 4 人并行）+ 各自 Claude Code 的权威分工与验收路线图。**完成本方案 = 达成本项目在无人类门禁前提下可达的全部目标（终态 RELEASE_CANDIDATE）。**

---

## 0. 现状裁决（一页真相）

经四路独立、以**实测为主**的核查（非轻信文档），结论如下：

| 维度 | 裁决 |
|---|---|
| **k8s 实现质量（督导问题1）** | **课程语境下相当高。** `kubectl kustomize` base+academy-eks 两个 overlay **本机实测 build 通过**（20 资源）；有**真实 EKS 部署证据**（`evidence/academy/ACADEMY-LIVE-001/`：真实镜像 digest、API 2/2 副本、Gateway/HTTPRoute Accepted/Programmed、TLS 请求返回 UP、删 Pod 恢复、滚动重启后 Redis session 存活、outbox 跨进程 PUBLISHED）。探针/资源/PDB/HPA/NetworkPolicy(屏蔽 IMDS)/StatefulSet(静态 PV)/Gateway API/多角色/安全上下文/TLS/Secret 外置全部正确自洽。**不是纸面 YAML。** |
| **愿景能否端到端跑出来（督导问题2）** | **骨架能跑，效果未证明。** `.\mvnw.cmd spring-boot:run` → `http://localhost:8080/app/aurora/` **免密钥**即可看完整产品闭环（Mock 模式）；后端抽样 83 测试全绿、前端 115 测试实跑全绿。真实 LLM 客户端（GLM/MiniMax/DeepSeek/MiMo）真实接入且真跑过。**但 G9 终验 5 项全部 UNASSESSED（从未端到端验收）；所有 AI 效果门标 `effectiveness=false`，人类盲评从未完成，唯一一次真实对比显示双核未优于单 prompt。** |
| **AI 核心真伪** | **真材实料的可运行骨架 + 诚实标注的未证明效果。** 双核编排、打断/停止/重规划(事件溯源)、主动唤醒(ShedLock)、时间感(真实天气 API)、11 类结构化 claim+10 维画像+provenance 反捏造门、授权编译的有界共鸣体、pgvector KNN——**都是真实实现**。缺口：默认跑 Mock+词法匹配+向量关闭；保真度/语义匹配/时效衰减只到"地板"未到"能力"。 |
| **账本可信度** | **诚实、无虚标。** 3 个 PASS(全在 G8 Academy)是硬部署证据；多处主动 walk-back 过度声明；无违规批量标 PASS。账本可当可信索引用。 |
| **前一个 Claude 干得怎么样** | **方向合规，火候过头。** 它严格执行操作者指令 v2 的 Phase 0（体验层 P0，有授权、执行规范：真浏览器验证+WCAG 取证+测试常绿）。**但连续 15 个检查点陷在 CSS/token 化/loading 态/像素级精修**（用户几乎无感），违反指令自己的"3 轮无实质差距即强制切换"纪律；同时把**无需人类门禁的高价值机器可做门**（旧页功能移植、事件可靠性、运行时角色、IaC、可观测、G9 demo）晾在一边。 |

**两大瓶颈：**
- **瓶颈 A（人类门禁，单点解锁最多）**：真实 LLM Provider 密钥 + 完成人类盲评。这一步解锁 G4/G5/G6 绝大多数 AI 质量项——是"解锁最多验收项的单点事件"。**需操作者尽快注入密钥。**
- **瓶颈 B（无门禁却被搁置的工程门）**：G2(事件可靠/运行时角色/Modulith)、G3(旧页移植/UX 质量)、G8(可观测/韧性/IaC/HPA 实测/备份)、G9(E2E/demo/追溯)。**这一片是四人并行的主战场。**

---

## 1. 终态定义（我们要打到哪）

**目标终态：`RELEASE_CANDIDATE_BLOCKED_BY_HUMAN_GATE`**（账本 `completion_rule.allow_release_candidate_blocked_by_human_gate: true`）。

> COMPLETE 需要 AWS 生产账户不可逆操作、PDPA/法务批准、心理专家审阅、真实用户研究同意——这些是**操作者本人**的人类门禁，Agent 不可冒充。因此对课程/工程交付而言，可达最大值是 RC。

**"完成本方案"的判定（= 全部我们能做的目标）：**
1. 所有**无人类门禁**的验收项在账本中为 `PASS`，且引用可复现证据。
2. G9 存在一份 **8–12 分钟可复现 demo**（脚本+素材+一键环境），覆盖产品差异化 / AI 深度 / 数据血缘 / K8s 运维四要素。
3. 存在**追溯矩阵**：账本每个必需项 → 当前可复现证据的映射，无隐藏 P0/P1。
4. 所有**人类门禁**都有一页式可执行清单（材料齐全，只差签字/注入），并在账本登记为 `BLOCKED_BY_HUMAN_GATE`。
5. 主干可构建、核心旅程可演示、数据可迁移、失败可回滚。

**明确保留为人类门禁（不阻塞 RC）：** `HG-SECRET-ROTATION`、`HG-PRODUCTION-ACCOUNTS`、`HG-PRIVACY-LEGAL`、`HG-PSYCHOLOGY-REVIEW`、`HG-REAL-USERS`、真机推送/签名、真实 provider 盲评的**人评环节**。

---

## 2. 四路并行工作流总览

| 路 | 名称 | 建议擅长 | 主攻账本项 | 代码边界（避免冲突） |
|---|---|---|---|---|
| **WS-A** | 云原生运维 & K8s 生产化（**督导问题1**） | 运维/云 | G8: OPS-OBSERVABILITY, OPS-RESILIENCE, EKS-IAC, ACADEMY-RELIABILITY, EKS-WORKLOAD 强化; G1: BASE-SUPPLY | `deploy/**`、`scripts/**`、`terraform/**`(新建)、`.github/**`、`prometheus.yml`、`Dockerfile`(CI 部分) |
| **WS-B** | AI 深度与效果证明（**愿景核心 + 督导问题2 的"效果"**） | AI/后端 | G4(全), G5(全), G6(全); ai-lab 评测 | `src/main/java/com/innercosmos/ai/**`、`ai-lab/**`、`src/test/**/evaluation/**` |
| **WS-C** | 产品体验收官 & 消灭双轨制（G3） | 前端/全栈 | G3: UX-SHELL, UX-LEGACY, UX-CONTRACTS, UX-QUALITY, UX-VISUAL-SYSTEM, UX-COMPLETE | `web/**`、`src/main/resources/static/**`、少量对应 `controller/**`(只加不改共用) |
| **WS-D** | 工程契约/数据权利/安全/移动 + **G9 终审集成** | 后端/集成 | G2(全), G7(全), G9(全); 免密钥 dev compose | `src/main/java/com/innercosmos/{event,scheduler,conversation,security,modulith}/**`、`mobile/**`、`deploy/compose/**`(dev 版) |

**冲突规避规则**：WS-B 与 WS-D 都动 `src/main/java`，但**按包切分**（ai/ 归 B；event/scheduler/conversation/security/modulith 归 D），公共类（如 `Entry`/DTO/config）改动需在群里先声明。WS-C 动前端为主，需要新后端端点时**只新增不改**已被 B/D 依赖的方法。

---

## 3. 各路详细任务

> 每条格式：`[账本ID] 任务 — 验收标准 → 证据落点`。阶段 P1=首两周、P2=中期、P3=收官。

### WS-A ｜ 云原生运维 & K8s 生产化

**目标**：把"课程可验证部署"抬升到"有观测、有备份、扩缩容/故障恢复被实证"的生产运维闭环，并补齐 IaC 与 CI/CD——**这是直接回应督导问题1、且最能加分的一路。**

**P1（首两周，全部无门禁）**
- `[OPS-OBSERVABILITY]` 部署 OpenTelemetry Collector + Prometheus + Grafana 的 **as-code** manifest（进 `deploy/k8s/overlays/` 或独立 `deploy/k8s/observability/`），接入 Spring Actuator/Micrometer；出 3 张 dashboard(应用健康/JVM/AI 成本&质量信号)+ 告警规则。→ `evidence/g8/OPS-OBSERVABILITY-001/`（截图+dashboard json+告警 yaml）
- `[OPS-RESILIENCE]` 在 **kind**(本机 80/443 被 kind 占用，正好用)上脚本化演练：滚动更新、删 Pod 恢复、（模拟）节点驱逐、优雅下线；记录 SLO/RPO/RTO 声明。→ `evidence/g8/OPS-RESILIENCE-001/`
- `[ACADEMY-RELIABILITY]` **HPA 实际压测**：注入 CPU 负载触发 2→N 扩容并录证据；顺手修 evidence 与 manifest 的 **HPA 2..6 vs 2..4 漂移**（对齐为一个值并更新 `ACADEMY-LIVE-001` summary）。→ `evidence/g8/HPA-LOAD-001/`

**P2**
- `[OPS-RESILIENCE]` **PostgreSQL 备份/恢复**：加 `pg_dump` CronJob + 一次真实 restore 演练（当前 hostPath 不耐久是硬缺口）。→ `evidence/g8/BACKUP-RESTORE-001/`
- `[EKS-IAC]` 新建 `terraform/`：commercial-sg 新加坡区域最小权限 EKS + 依赖，做到 `terraform validate` + `terraform plan` 干净（真实 `apply` 是人类门禁 `HG-PRODUCTION-ACCOUNTS`）。→ `evidence/g8/EKS-IAC-001/`（plan 输出，脱敏）
- `[BASE-SUPPLY]` CI/CD 流水线（`.github/workflows/`）：build→测试→SBOM→依赖/SAST/secret/IaC/容器扫描**门禁化**→镜像签名。当前部署全靠手动 PowerShell，需要流水线。→ `evidence/m1/M1-BASE-001/`（补 CI 运行链接/日志）

**P3**
- 配合 WS-D 的 G9：把上述运维演练**由非作者 Agent 独立走一遍**（`OPS-*` 账本要求 non-author 演练），产出运维 runbook。→ `evidence/g9/FINAL-OPERATIONS-001/`

**保留人类门禁**：真实 AWS 账户 apply、真实节点池扩缩成本。

---

### WS-B ｜ AI 深度与效果证明

**目标**：让 AI 从"降级默认形态"跨到"愿景质量"，并把"效果未证明"这个最致命缺口**在密钥到位后立刻闭合**。这是愿景的心脏，也是督导问题2"效果"的答案。

**P1（无门禁，为密钥到位做满准备）**
- `[RETRIEVAL-QUALITY][MATCH-MULTI]` **打通向量语义**：让 `MemoryEmbeddingIndexService` 与 capsule 匹配在 `MEMORY_EMBEDDING_ENABLED=true`+真实 key 时走 pgvector 语义（现默认 `DisabledMemoryEmbeddingClient`+词法子串）；词法保留为 fallback。→ `evidence/innovation/INNO-INNER-00X/`
- `[CAPSULE-COMPILER]` 修一致性 gap：授权更新时 `CapsuleContextRegenerator` 只改可变 personaPrompt、**不触发 genome 重编译**——改为触发重编译或明确失效。→ 证据 + 回归测试
- `[AURORA-QUALITY][CAPSULE-RUNTIME]` **评测 harness 补全**：把 `OptionalLlmJudge`(现永远 NOT_RUN)接上；`psychology_compare` 的硬编码 f-string 基线换成真实生成；双核 vs 单 prompt 对比 harness 做到"Mock 可跑、真实 key 即插即用"。→ `ai-lab/` + `evidence/innovation/INNO-EVAL-00X/`
- `[MEMORY-LIFECYCLE][PROFILE-MODEL]` claim 抽取 Slice 4：实体/时间/关系归一化 + 语义冲突检测 + 不确定性标注；claim/画像加**时效衰减/TTL**（现只有检索时衰减）。评测集从 9-10 例扩到 ≥30-50 例含难例反例。
- `[PROFILE-PROPAGATION]` **FORGET 传播剩余四面**（prompt 缓存/sync-queue/analytics/备份生命周期的遗忘审计）——隐私承诺完整性，优先。

**P2（密钥到位后立即，最高优先——这是解锁最多验收项的单点）**
- `[AURORA-DUAL-KERNEL][AURORA-CHOREOGRAPHY][AURORA-TEMPORAL][AURORA-SELF][AURORA-QUALITY]` 用真实 provider 跑完积压的 **pairwise / 盲测 / 校准评测**；证明双核>单 prompt、共鸣体保真度、主动性得体度。人评环节打包成一页式（人类门禁），自动化对比与 fresh-context Agent 复核先跑。
- `[CAPSULE-SAFETY][SIMULATOR-ASSET]` 真实保真/泄露评分从"结构清单"升级为可解释 score；对抗反问 + 纵向漂移评测场景。

**P3**：AI 深度进 G9 demo（数据血缘 + AI 深度两要素由 WS-D 集成）。

**保留人类门禁**：`REAL-PROVIDER-CREDENTIALS`(操作者注入 key)、真实盲评的**人评打分**。

---

### WS-C ｜ 产品体验收官 & 消灭双轨制

**目标**：**先把 Phase 0 体验层"关闭"**（别再抛光），再做真正有价值的"旧页功能移植 + 双轨制消灭 + 质量门"。

**P1（先收口体验 P0，止损像素级精修）**
- `[UX-VISUAL-SYSTEM]` 补齐操作者指令自己的退出标准里**唯一没做完**的证据：**日间+夜间两时段浏览器截图**（cp16 一直卡在这，用真实截图而非 getComputedStyle 数值）；透光母题统一 + 本地字体打包(霞鹜文楷/思源宋体，注意 CSP/离线) + Lighthouse>85 + 移动端断点验收。**做完即宣布 UX-VISUAL-SYSTEM 收口，不再进精修。** → `evidence/experience/`
- `[UX-LEGACY]` 死按钮回归门固化（Playwright 扫描已有，补成常绿门）。→ `evidence/experience/dead-button/`

**P2（真正的高价值：旧页移植，账本白纸黑字列出的真实功能空洞）**
- `[UX-SHELL][UX-LEGACY]` 逐个把 13 个遗留静态页的**独占功能**用新设计系统移植进 AppShell，然后下线对应旧页：
  - relations/social：关系温度/健康/时间线/群组（现 `web/src/api.ts` **零 API-client**，最大空洞）
  - letters：outbox 已做，补 drafts/archived/线程视图
  - capsule-create：补齐向导硬编码的非 boundary 字段
  - aurora-chat：语音输入
  - 其余 parity 清单见账本 UX-SHELL remaining
  - 节奏：移植→浏览器验证+截图→下线旧页→提交。→ 每页一份 `evidence/campaign-a/`
- `[UX-CONTRACTS]` 生成式 typed API client 全覆盖核心域。

**P3**
- `[UX-QUALITY][UX-COMPLETE]` Playwright/视觉回归/a11y/i18n/性能预算跑进核心路由；双语(zh-CN/en-SG)+WCAG 2.2 AA+reduced-motion 全五空间验收。→ `evidence/g3/UX-COMPLETE-001/`

**保留人类门禁**：无（本路几乎全机器可做，是 RC 的必经硬工作量）。

---

### WS-D ｜ 工程契约/数据权利/安全/移动 + G9 终审集成

**目标**：关掉 G2 三个"从未评估"的纯工程门，补 G7 机器可做部分，并**拥有 G9 终审集成**（demo/E2E/追溯矩阵——课程最终证明）。

**P1（G2 三个无门禁的 UNASSESSED 门，优先）**
- `[ARCH-MODULES]` Spring Modulith 可执行模块边界 + 架构测试。→ `evidence/g2/ARCH-MODULES-001/`
- `[EVENT-RELIABLE]` 事务性 outbox→(SQS 可替换抽象)→幂等消费→重试→DLQ 重放**全链路**验证（本地/Testcontainers 可做；academy 用 JDBC outbox 已有证据）。→ `evidence/g2/EVENT-RELIABLE-001/`
- `[RUNTIME-ROLES]` API/AI worker/event worker/scheduler/migration 五角色独立运行与伸缩验证(compose/kind)。代码里角色装配已真实存在，补验证证据。→ `evidence/g2/RUNTIME-ROLES-001/`
- **免密钥 dev docker-compose**：现两个 compose 都 fail-closed，`docker compose up` 开箱即失败。加一个 `deploy/compose/dev.yml`(H2/pg 无 TLS + Mock AI)让 demo 能一键起。→ 直接服务 G9。

**P2**
- `[API-CONTRACT]` v1 之外公共域纳入 OpenAPI；一致游标分页；多 Pod SSE soak(延迟/心跳百分位 + Redis Stream consumer-group 重投递)。
- `[DATA-RIGHTS]` DataUseGrant 升级为跨域同意中心(替代 consentScope 字符串)。
- `[SKILL-RUNTIME][SKILL-PRODUCT]` 3 个心理 Skill 推到真实内容质量 + 危机/不适用/拒绝/撤回四旅程完整可走 + SG 本地资源；专家审阅材料打包(人类门禁)。
- `[MOBILE-NATIVE]` 原生 OIDC/PKCE 客户端 + API-origin 信任校验 + 深链模拟器验证(真机推送/签名留人类门禁)。

**P3（G9 收官——集成全部四路成果）**
- `[FINAL-E2E]` Web/PWA/移动容器三面核心旅程 E2E 自动化通过。
- `[FINAL-DEMO]` **8–12 分钟可复现 demo**：脚本+素材+一键环境(用 P1 的 dev compose)，四要素齐全(产品差异化[WS-C]/AI 深度[WS-B]/数据血缘[WS-B]/K8s 运维[WS-A])。→ `evidence/g9/FINAL-DEMO-001/`
- `[FINAL-TRACEABILITY]` 追溯矩阵：账本每必需项→证据映射，无隐藏 P0/P1。
- `[FINAL-OPERATIONS]` 配合 WS-A：运维手册由非作者 Agent 独立走一遍。

**保留人类门禁**：`HG-PSYCHOLOGY-REVIEW`、真机推送/签名、`FINAL-USABILITY`(真实非作者用户)。

---

## 4. 跨路依赖与汇合

```
P1: WS-A/B/C/D 几乎完全并行(边界已切分)。
    关键外部动作: 操作者尽快注入真实 LLM key → 解锁 WS-B 的 P2(单点解锁最多)。
P2: WS-B 密钥评测(依赖 key) ‖ WS-A 备份/IaC/CI ‖ WS-C 旧页移植 ‖ WS-D G2/G7。
P3(汇合): WS-D 拥有 G9 集成, 消费:
    - WS-A → K8s 运维演示 + 非作者运维演练
    - WS-B → AI 深度 + 数据血缘演示素材
    - WS-C → 产品差异化旅程(五空间齐平)
    - WS-D → dev compose 一键环境 + E2E + 追溯矩阵
```

**唯一强串行依赖**：G9 demo(WS-D P3)需要其余三路 P2 基本收口。其余全部可并行。

---

## 5. 协作纪律（沿用 `loop-goal-directive.md` §3，全员适用）

1. **证据先于断言**：UI 改动必须真实浏览器验证+截图入 `evidence/`；后端 focused test 起步、风险点跑全量。基线只增不减(当前 Java ~835、web 115 全绿)。
2. **每个检查点**：描述性提交(含 `Co-Authored-By` trailer)→更新 `evidence/`→更新账本 `complete-product-acceptance.yml` 与 `single-session-state.yml`→推送(禁 force-push)。
3. **不得虚标**：不降低断言、不删测试、不把失败改成 known limitation 关门；不把未独立验收项标 PASS；"非作者评审"可用 fresh-context Agent 并注明，**真实人类门禁不可用 Agent 冒充**。
4. **每 5–10 检查点对账一次** `remaining` 字段(本项目历史多次文档超前于实现)。
5. **推送前** `pwsh scripts/scan-secrets.ps1` 干净；密钥只经环境变量注入。
6. **分支**：各路开自己的 `feat/ws-a-*` … 分支，PR 汇入 main；main 保持可构建可演示。
7. **本机事实**：JDK 21 已装；Node v24 在 `C:\Program Files\nodejs`(Git-Bash 需先 `export PATH`)；用 npm；Docker+Redis 容器 `inner-cosmos-redis`；本机 80/443 被 kind 占用(WS-A 演练正好用 kind)。

---

## 6. 人类门禁清单（需操作者/组长处理，按优先级）

| 优先 | 门禁 | 动作 | 解锁 |
|---|---|---|---|
| **P0** | 真实 LLM key | 注入 `LLM_PROVIDER`+`<PROVIDER>_API_KEY`(GLM/MiniMax/DeepSeek 之一) | **WS-B 全部 AI 质量项**——单点解锁最多 |
| P1 | `HG-SECRET-ROTATION` | 外部密钥吊销/轮换/独立签字 | G0 SEC-ROTATION |
| P2 | 真实盲评人评 | 按 WS-B 打包的一页式给评审打分 | G4/G6 质量 PASS |
| P2 | `HG-PSYCHOLOGY-REVIEW` | 心理专家审阅危机/Skill | G7 SKILL-PRODUCT |
| P3 | `HG-PRODUCTION-ACCOUNTS` | AWS 真实 apply / 真机签名 | EKS-IAC apply / MOBILE 真机 |
| P3 | `HG-PRIVACY-LEGAL` / `HG-REAL-USERS` | PDPA/跨境/真实用户同意 | SG-RELEASE / FINAL-USABILITY |

> 门禁不阻塞主线：为每项准备一页式可执行清单，登记 `BLOCKED_BY_HUMAN_GATE` 后继续所有不依赖它的工作。

---

## 7. 建议里程碑

- **M1（约 2 周）**：四路 P1 收口。K8s 有观测+韧性演练+HPA 实测；AI 向量打通+harness 就绪；体验 P0 关闭+死按钮门；G2 三门关闭+dev compose 可一键起。**操作者本阶段内注入 key。**
- **M2（约 2–3 周）**：AI 真实评测跑完(key 到位)；备份/IaC/CI；旧页移植过半;G7/移动/契约推进。
- **M3（约 1–2 周）**：G9 集成——E2E 自动化 + 8–12 分钟 demo + 追溯矩阵 + 非作者运维演练。宣布 **RELEASE_CANDIDATE_BLOCKED_BY_HUMAN_GATE**。

---

## 8. 立即可做的第一批（kickoff，每路 3 件）

- **WS-A**：① kind 起集群跑通 base overlay；② 加 Prometheus/Grafana manifest 出第一张 dashboard；③ 修 HPA 2..6/2..4 漂移。
- **WS-B**：① 打通 `MEMORY_EMBEDDING_ENABLED` 语义检索路径；② 把 `OptionalLlmJudge` 从 NOT_RUN 接上；③ 准备真实 provider 评测一页式(等 key)。
- **WS-C**：① 补日间+夜间两时段真实截图收口 UX-VISUAL-SYSTEM；② relations/social 建 API-client;③ 死按钮回归门常绿。
- **WS-D**：① Spring Modulith 架构测试(ARCH-MODULES)；② 写 `deploy/compose/dev.yml` 免密钥一键起；③ EVENT-RELIABLE outbox→DLQ 全链路测试。

---

_本方案完成即等价于：在无操作者人类门禁前提下达成 `goal-objective.md` 全部结果契约，账本收敛到 RELEASE_CANDIDATE。人类门禁项材料齐全、只差签字/注入。_
