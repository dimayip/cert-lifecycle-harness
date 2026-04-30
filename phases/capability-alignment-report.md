---
doc: capability-alignment-report
status: v0.5 · 4 案例推演后的 58 卡点归并 12 主题一次落地（对应 capability-matrix.md v0.5）
updated: 2026-04-23
parent: ../SKILL.md
reviewed_files:
  - SKILL.md
  - phases/00-intake-checklist.md
  - review-guides/L1-execution-review.md
  - review-guides/L2-strategy-review.md
  - review-guides/L3-decision-review.md
  - review-guides/self-review-checklist.md
  - scripts/readonly/TEMPLATE.sh
---

# 🧭 声明兑现缺项报告（Alignment Report · v0.1）

> 本报告遵循 [`capability-matrix.md §4.2`](./capability-matrix.md) 工作流输出。
> **只给事实，不自动改 SKILL.md / 不自动改 phases**；由人类决定补齐优先级。
> 反审范围：骨架阶段的"声明兑现"检查（决策 4B：分级反审 · 高频红 / 中频黄 / 低频白）。
> **不做能力完备性审查**。

---

## 1. 执行摘要

> **版本迷走提示**：下面的 3 红 4 黄 11 绿 是 v0.1 首次反审的结果。v0.2 已修复全部 3 项红项，现状为 0 红 4 黄 14 绿。
> 下次反审请重新类型扫描，不要基于本版本的计数继续报告。

| 指标 | v0.1 | v0.2 |
|------|------|------|
| 🔴 红 | 3 | **0**（R1/R2/R3 已收收）|
| 🟡 黄 | 4 | 4（Y1/Y2/Y3/Y4 维持，等推演触发）|
| ⚪ 白 | 0 | 0 |
| ✅ 通过 | 11 | 14（+3：R1/R2/R3 对应项由红转绿）|

---

## 付录：v0.1 原始执行摘要

| 指标 | 数量 |
|------|------|
| 🔴 红（契约违反 / 高频骨架缺项） | **3** |
| 🟡 黄（已声明但未落地） | **4** |
| ⚪ 白（低频，骨架阶段不反审） | 0 |
| ✅ 通过（声明与落地一致） | **11** |

**整体结论**：骨架阶段**基本合格**（11 项兑现），但有 **3 项契约违反**需要先修（性质上是"Skill 自己打自己的脸"，不修会误导使用者）。中频 🟡 项按正常节奏走，不阻断进度。

---

## 2. 🔴 红项详情（必须先修）

### R1 · Skill 声明"不绑定任何云厂商"，但 intake-checklist 写死 5 家云多选框

**声明来源**：
- [`SKILL.md § 0.1`](../SKILL.md) "跨厂商 API 命名风格差异说明"
  > ⚠️ 本 Skill **不绑定任何特定云厂商**；Agent 在任何云上使用 API 都必须先跟用户确认并查阅该厂商当前官方文档。

**矛盾落地**：
- [`phases/00-intake-checklist.md §2.1 基础设施栈`](./00-intake-checklist.md)：
  ```markdown
  **CDN**：
  - [ ] 腾讯云 CDN
  - [ ] 阿里云 CDN
  - [ ] Cloudflare
  - [ ] AWS CloudFront
  - [ ] 自建（请说明：{{}}）
  - [ ] 不使用 CDN
  ```
- 把 4 家具体云厂商写死为多选框，隐式暗示"Skill 主打这 4 家"，违反 § 0.1 的声明。

**影响**：
- Agent 在华为云/火山引擎/京东云等场景下会**陷入 intake-checklist 的预设列表**，让用户选"其他" → 失去四段式推荐的信息收集价值。
- 这与 Skill 原始设计原则"不假设客户环境"正面冲突。

**建议修法**（不在本报告内执行）：
- 把 §2.1 的多选框改为**开放式填空 + 示例列表**：
  ```markdown
  **CDN**：请填写你们使用的 CDN 厂商名称（若自建请写"自建 Nginx/HAProxy/..."；若不使用请写"无"）
  > 示例：腾讯云 CDN / Cloudflare / 自建 / 多云混合
  ```
- 等效处理 `负载均衡 / 网关` 和 `容器编排 / 证书自动化` 两小节。

---

### R2 · self-review-checklist 缺写 API 六闸门自检，与 SKILL.md § 6 不一致

**声明来源**：
- [`SKILL.md § 6`](../SKILL.md)：
  > **受托执行写 API 前的强制自检**（对应 § 0.2 六条闸门，任一未勾不得执行）：
  > - [ ] 档位判定：本次是 Import 还是 Modify？
  > - [ ] 闸门 1~6（略）
  > - [ ] 凭证：使用环境变量/临时 token，绝未落盘？

**矛盾落地**：
- [`review-guides/self-review-checklist.md`](./review-guides/self-review-checklist.md) 的 D 类（Harness 原则）只覆盖 HP-3~HP-7，**完全没有六闸门自检**章节。
- 这意味着：Agent 交付前按 self-review-checklist.md 跑一遍**通不过写 API 闸门核验**，与 SKILL.md § 6 承诺的"自检不过不交付"形成断层。

**影响**：
- 矩阵 D4.4 / D4.5 (Import / Modify 类写 API 代为执行) 的**落地防护环节缺位**。
- 骨架阶段若不修，未来推演进入"Agent 代执行 Modify 类"场景会直接踩空。

**建议修法**（不在本报告内执行）：
- 在 `self-review-checklist.md` D 类后新增一个独立章节 `F. 受托执行写 API 前的强制自检`，一字不差照抄 SKILL.md § 6 的 8 项闸门核验。
- 在"失败处理"表新增一行：`F 类失败（闸门未满足）→ 不得代为执行，改为生成脚本交人类`。

---

### R3 · intake-checklist §3 "可选信息"直接给默认值，绕过 § 2.1 四段式推荐协议

**声明来源**：
- [`SKILL.md § 2.1`](../SKILL.md) 硬边界：
  > - 推荐必须**显式标注**"基于行业最佳实践"，不得伪装成已知事实
  > - 替代方案**至少 1 个**，且附适用场景

**矛盾落地**：
- [`phases/00-intake-checklist.md §3 "可选信息"`](./00-intake-checklist.md)：
  ```markdown
  - [ ] 证书有效期偏好：`1 年` / `90 天(ACME 默认)` / `其他`
  - [ ] 密钥算法偏好：`RSA-2048` / `RSA-3072` / `ECC P-256` / `国密 SM2`
  - [ ] 是否启用 OCSP Must-Staple？
  - [ ] 是否需要 CT 预证书监控告警？
  ```
- 问题：裸列选项 + "不填我用默认最佳实践"的隐式承诺，**没有给出推荐 + 理由 + 替代**，直接绕过四段式协议。

**影响**：
- 用户面对 "密钥算法偏好：RSA-2048 / RSA-3072 / ECC P-256 / SM2" 这种裸问，**认知负担直接拉满**（决策 4 选项 vs 阅读 30 秒的四段式推荐）。
- 违反 SKILL.md § 2.1 "字段适用性表"的分类：密钥算法、证书有效期、OCSP Stapling 都属于 **"有通用最佳实践"** 类，应该走四段式推荐。

**建议修法**（不在本报告内执行）：
- 把 §3 改写为 4 个四段式推荐小节，每个字段独立：
  ```markdown
  ### 3.1 密钥算法

  【推荐】 ECDSA P-256
  【理由】
    - 与 RSA 2048 安全强度相当，握手开销更小
    - 主流浏览器、CDN、LB 均已支持
    - 证书体积更小，对移动端延迟更友好
  【替代】
    - RSA 2048：兼容极老客户端（老 JDK < 8u161、部分 IoT）
    - SM2（国密）：金融、政企合规场景
  【请确认】 你们是否有老客户端兼容、国密合规、HSM 限制等特殊需求？
  ```

---

## 3. 🟡 黄项详情（已声明但未落地，中频 · 可在推演阶段补齐）

### Y1 · 国密 SM2 场景（对应矩阵 D2.5 / D5.4）

**已声明**：
- SKILL.md § 0.1 警示国密"接口差异大"
- SKILL.md § 2.1 密钥算法示例列出 SM2
- intake-checklist §2.3 有"是否有国密要求"一问

**未落地**：
- `L2-strategy-review.md` 的 CA 选型候选对比表**没有国密专属列**（CA 候选全部是商业 CA）
- 合规章节没有双证书并行方案的字段
- Phase 2 CA 选型 ADR 无国密分支
- 反审对照组合 **M4（任意 × 国密 × 任意 × 任意 × 国密合规）** 无支撑

**建议**：首次推演若碰到金融/政企客户，专项补一份 `phases/ca-selection-adr-gm.md` 插件。

### Y2 · API 网关 / Serverless 拓扑（对应矩阵 D1.6）

**已声明**：
- SKILL.md § 0.1 在"适合只读 API 自动化的典型场景"里提了一句 "API Gateway 自定义域名的证书绑定关系"

**未落地**：
- intake-checklist §2.1 基础设施栈**没有 API 网关选项**
- L2 / L1 review 模板无 API 网关相关字段
- 反审对照组合 **M6（Serverless × ...）** 无支撑

**建议**：在推演第一个"API 网关场景"客户时专项补齐，不阻断骨架。

### Y3 · 等保合规 / 金融牌照字段（对应矩阵 D5.2 / D5.3）

**已声明**：
- SKILL.md § 2.1 字段适用性表明示"等保级别、金融牌照要求"为"合规强约束"

**未落地**：
- intake-checklist §2.3 只问"是否涉及金融/支付/医疗"**但不细化等保级别/具体牌照类型**
- L2 review 合规章节是占位符 `{{PCI-DSS / 等保 / GDPR}}`，无分级字段

**建议**：首次推演遇金融客户时，专项补 intake 细化问题 + L2 合规表。

### Y4 · 私有 CA / HSM 场景（对应矩阵 D2.4）

**已声明**：
- SKILL.md § 0.1 警示"私有 CA、HSM 等特殊场景必须向用户单独确认"

**未落地**：
- intake-checklist §2.1 "容器编排 / 证书自动化"有"企业内部 PKI"选项，但仅止于多选框，无后续分支
- L2 review 无私有 CA 链路验证、HSM 密钥生成字段
- 反审对照组合 **M5（任意 × 私有 CA × 复杂 × 任意 × 任意）** 无支撑

**建议**：推演内网 PKI 客户时专项补。

---

## 4. ✅ 通过项（声明与落地一致）

| # | 矩阵声明 | 落地位置 |
|---|---------|---------|
| P1 | D4.1 零授权 → 命令清单退化 | SKILL.md § 5 Phase 1 分支 B ✅ |
| P2 | D4.2 只读授权 → Agent 代为执行 | SKILL.md § 0.1 + § 5 Phase 1 分支 A ✅ |
| P3 | D4.3 探测类 | SKILL.md § 0.1 🟡 行 ✅ |
| P4 | D4.4 / D4.5 Import / Modify 代为执行 | SKILL.md § 0.2 三档 + 六闸门 ✅ |
| P5 | D4.6 Delete 永不代为执行 | SKILL.md § 0 + § 0.2 + § 5 Phase 5 分支 C ✅ |
| P6 | 四段式推荐协议本体 | SKILL.md § 2.1（有完整示例 + 硬边界 + 适用性表）✅ |
| P7 | 信息等级与占位符 | SKILL.md § 2 + intake-checklist 顶部说明 ✅ |
| P8 | 分层 Review 时间预算 | SKILL.md § 3 + L3/L2/L1 三份模板 ✅ |
| P9 | Layer 0 七问验收 | SKILL.md § 1 ✅ |
| P10 | 每 Phase 停等人类 | SKILL.md § 5 每阶段 ⛔ 标注 ✅ |
| P11 | 脚本 TEMPLATE 结构 | scripts/readonly/TEMPLATE.sh + SKILL.md § 4 ✅ |

---

## 5. 补齐优先级建议（供你决策，非自动执行）

```
优先级 1（阻断式）：R1 + R2 + R3 三个 🔴 红项
  ├─ 性质：Skill 自己和自己不一致，修起来小（每项 < 30 min），收益大
  ├─ 不修后果：任何使用者首次用本 Skill 都会踩中"声明与实现冲突"
  └─ 建议：进入下一轮前先批量修完

优先级 2（推演驱动）：Y1~Y4 四个 🟡 黄项
  ├─ 性质：已声明但未落地，属于"能力储备"缺口
  ├─ 不修后果：碰到对应场景（金融/国密/API 网关/私有 CA）时 Agent 会降级到裸问
  └─ 建议：在第一个碰到对应场景的真实推演案例中专项补齐，不做前置补齐

优先级 3（未来路线图）：矩阵 §1.3 "明示未落地"之外的能力
  └─ 暂不登记本报告，等推演产生需求再进入矩阵 §5「待决定的能力边界」
```

---

## 6. 后续动作（请你决策）

请从以下三个方向中选一个，我按你的选择推进：

**方向 A**（推荐）：**先修 R1~R3 三个红项**
- 我分别修改 `phases/00-intake-checklist.md` 和 `review-guides/self-review-checklist.md`
- 修完后更新 `capability-matrix.md` 的 `updated` 字段
- 预估 3 次编辑 + 1 次 review

**方向 B**：**暂停，进入真实案例推演**
- 接受 R1~R3 已知不一致，直接跑案例
- 风险：推演过程会被"Skill 自打脸"干扰

**方向 C**：**只修 R1**（intake 去云厂商绑定）
- R1 是最刺眼的契约违反，单修后能让 Skill 骨架"表里如一"
- R2/R3 作为 backlog 暂存

---

## 7. 变更历史

| 日期 | 版本 | 变更 |
|------|------|------|
| 2026-04-23 | v0.1 | 首次反审落盘：扫描 SKILL.md + 7 份骨架文件，输出 3 红 4 黄 11 绿 |
| 2026-04-23 | v0.2 | R1/R2/R3 全部已修复。R1：intake §2.1 改为开放式填空 + 示例；R2：self-review-checklist 新增 F 类写 API 闸门自检；R3：intake §3 改为四段式推荐协议（4 字段）。中频 4 黄项维持原状，等推演触发 |
| 2026-04-23 | v0.3 | 新增能力：DNS 掌控权作为独立第 6 维（D6）加入矩阵。同步修改：① `capability-matrix.md` 升级到 v0.3，§1.1 新增 3 条声明（DNS 探针模式 / 作用域原则 / 推理透明度 M1-b），§2 新增 D6 及 4 档取值，§3.1 新增高频组合 H5；② `SKILL.md § 0.1` 只读 API 表格 DNS 条目显式化，新增"DNS 探针与基础设施反推"小节（含能力、作用域原则 N2′、M1-b 透明度原则、隐私硬边界）；③ `SKILL.md § 2` 🔴 清单新增 "DNS 记录全貌或 DNS 只读授权"；④ `SKILL.md § 5 Phase 1` 增加 DNS 探针子流程到分支 A，分支 B 增加 DNS 降级项；⑤ `intake-checklist §1.2` 增加 DNS 推荐提示，`§2.1` 新增 "DNS 服务商与授权等级" 字段。落地决策链：A3 + B2 + C1 + M1-b + N2′。本次变更**不触发**新红项；原 4 黄项维持。 |
| 2026-04-23 | v0.4 | 新增能力：**SAN 闭包发现**（证书↔域名图遍历，从种子证书/域名出发迭代展开至不动点）。同步修改：① `capability-matrix.md` 升级到 v0.4，§1.1 新增 5 条声明（闭包发现本体 + E2-b′ 收敛上限 + E4-b 通配符展开分层 + E6-c 跨 zone 硬边界 + E3-c 批量勾选），§2 D2 新增 D2.6（多域单 zone）/ D2.7（多域跨 zone），§3.1 新增高频组合 H6（跨 zone 多域证书）；② `SKILL.md § 0.1` 新增"SAN 闭包发现"小节（图模型 / 收敛条件 / 证书类型影响 / 通配符展开分层 / 跨 zone 硬边界 / E3-c 呈现格式 / 隐式决策禁令）；③ `SKILL.md § 5 Phase 1` 重写为以闭包迭代为核心的流程（Step 1-4 迭代循环 + 分支 A/B 均纳入闭包）；④ `intake-checklist §1.1` 改为"种子证书"哲学，顶部新增 E5-b 种子模式说明；§1.2 从"必需"降为"可选"。事实校准：多域证书 SAN 为多个确定 FQDN 而非多个通配符；.cn 与国密无必然关联。落地决策链：E1-a + E2-b′ + E3-c + E4-b + E5-b + E6-c，E7 撤回。本次变更**不触发**新红项；原 4 黄项维持。 |
| 2026-04-23 | v0.5 | 结构性升级：基于 4 案例推演（F1 泛域 `dns.tencentcloudapi.com` / F2 小型多域 `www.qq.com` / F3 极复杂跨 zone `cloud.tencent.com` 13 SAN / 5 zone / F4 真单域 `i.qq.com`，全部基于 crt.sh / openssl 获取的真实 SAN 事实）暴露的 **58 个骨架卡点**，**归并为 12 主题一次全上**（β 路线）。同步修改：① `SKILL.md § 0` 新增 § 0.3 复杂度分流（Fast / Standard / Full Path），§ 0.1 新增绑定点定义 / cert_role 维度 / 反向扩展启发式 / 部分授权降级 / 影子证书溯源指引，§ 5 加导航说明 + Phase 方法论文件索引 + 路径裁剪映射；② 新建 6 份 phases playbook（`01-inventory-guidance.md` / `02-scope-lock-and-reflow.md` / `03-risk-assessment-playbook.md` / `04-planning-playbook.md` / `05-dry-run-matrix.md` / `06-verify-rollback-playbook.md`）；③ 新建 `phases/runbook-templates/` 目录，含 JKS / Nginx / K8s Secret / CDN 手工 4 份模板 + README 索引；④ `intake-checklist` 顶部插入"第零部分·路径分流"，§1.3 补内部 SLA 缓冲，§2.2 补 Full Path 审批矩阵，§2.4 补 CA 账号归属 + 采购流程时长，§3.4 之后新增 §4 Full Path 专属字段（资产分类 / 未知资产追踪 / 合规细化 / 提议-复议状态机 / 关键路径 / 协调演练授权）；⑤ `capability-matrix.md` 升至 v0.5，§1.1 追加 30+ 条新声明，§2 新增 D7 资产分类 / D8 cert_role / D9-D12 组织能力 / D13 覆盖率异构度 共 7 个维度，§3.1 新增 H7 Fast Path + H8 收购遗留 Full Path 两个高频组合；⑥ `review-guides/self-review-checklist.md` 新增 G 类（七问闭环 / 复杂度分流 / 裁剪留痕 / 委托决策日志 / 提议-复议完整性 / 拒绝优雅降级 / 事实校准红线），"失败处理"表加 G 类，"通过声明"模板加 v0.5 新行。落地路线：**β 一口气全上，12 主题实核全部完成**。本次变更**不触发**新红项；原 4 黄项维持，待对应案例推演触发。 |
