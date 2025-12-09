# TRAE 国际版 Git Agent 操作说明书（WSL 2 Ubuntu 24.04 敏捷协作完整版）

# 前言

本说明书以「WSL 2 Ubuntu 24.04 为运行核心、TRAE 国际版为开发入口」，深度整合原 Git Agent 核心能力（多仓库联动、敏感数据脱敏、分支快照回滚等）与敏捷项目管理理念，适配跨终端全栈多 Agent 大型 AI 应用开发需求，实现「需求-开发-测试-部署」全流程自动化与团队协作规范化，兼顾效率、安全与可追溯性。

# 一、核心机制与优化闭环（保留原框架+敏捷增强）

## 1. 核心定位（继承原逻辑）

以 **TRAE 国际版为唯一管控入口、GitHub 为多仓库存储核心、WSL 2 为运行载体**，通过 MCP 协议封装 GitHub API 及 Git 原生命令，新增「敏捷任务联动」「多 Agent 分工适配」模块，形成「合规管控-敏捷协作-故障回滚」的完整闭环，人工仅把控 main 分支合并与高风险操作，兼顾效率与生产安全。

## 2. 核心工作原理（优化后闭环）

在原 5 大模块基础上融入敏捷任务流，形成 7 步协同闭环：

1. **敏捷需求同步与多库 Issue 生成**：Jira/GitHub Projects 中的用户故事同步至 TRAE，Git Agent 自动解析并向关联多仓库创建标准化 Issue，关联 Sprint 与优先级；

2. **Agent 任务分配与多库分支协同**：TRAE 看板触发任务分配，Git Agent 向对应 Agent 推送指令，自动完成多仓库分支克隆、创建（按规范命名）与依赖关联；

3. **AI 开发+前置评审+代码脱敏**：Agent 完成开发后，Git Agent 联动评审 AI 做前置校验，自动脱敏密钥、私钥等敏感数据，生成脱敏日志；

4. **合规提交与 Commit 信息规范化**：Git Agent 提供 Commit 模板，强制关联 Issue ID 与任务类型，不符合规范则拦截提交；

5. **分级 PR 提交+双向同步审核**：按「研发→测试→预部署→main」分级提交 PR，TRAE 与 GitHub 实时同步审核状态，评审意见关联至敏捷任务；

6. **合并执行+快照备份+状态同步**：人工确认后 Git Agent 完成合并，自动创建分支快照；同步更新 TRAE 看板与敏捷工具任务状态为「已完成」；

7. **异常回滚与全流程追溯**：合并异常时一键回滚至快照版本，回滚日志关联任务与 PR，支撑问题复盘。

## 3. 关键特性（保留原功能+敏捷适配）

- **多仓库联动协同**：绑定前端/后端/合约等多仓库，实现 Issue、分支、PR 的跨库同步与依赖校验，适配多 Agent 分工；

- **敏捷任务深度联动**：接入 Jira/GitHub Projects，支持用户故事→任务→分支→PR 的全链路关联，自动同步进度；

- **AI 前置代码评审**：校验代码规范、安全漏洞（如合约重入风险）、性能优化，提前规避开发风险；

- **敏感数据脱敏**：自动识别并替换敏感信息，原始数据加密存储至 WSL 2 加密目录，杜绝泄露；

- **分支快照与一键回滚**：关键节点自动创建快照，合并异常时快速回滚至稳定版本，降低故障影响；

- **全流程日志追溯**：记录分支操作、评审结果、脱敏记录、回滚行为，关联任务与 PR，支撑审计与复盘。

# 二、Git Agent 架构框架（补充敏捷与多Agent层）

## 1. 整体架构（五层架构+新增敏捷协作层）

```plaintext

┌─────────────────────────────────────────────────────────────┐
│  交互层（Interaction Layer）                                │
│  - TRAE 国际版看板：敏捷任务、PR状态、回滚操作入口          │
│  - 团队同步面板：每日站会记录、进度汇总                     │
│  - 敏捷工具接口：GitHub Issues/Projects 数据双向同步，支持任务发布与状态回传 │
│  - 智能冲突解决：提供冲突预警、自动合并建议及手动解决引导   │
├─────────────────────────────────────────────────────────────┤
│  敏捷协作层（Agile Collaboration Layer）                    │
│  - 需求解析模块：GitHub Issue 自动识别与任务拆解，关联多Agent职责 │
│  - 任务分配模块：基于 Issue 标签和 Agent 角色智能分配任务，同步至 TRAE 看板 │
│  - 进度同步模块：分支/PR/Issue 状态自动映射为任务进度，实时更新 │
├─────────────────────────────────────────────────────────────┤
│  核心层（Core Layer）                                       │
│  - 多库分发模块：生成多仓库Issue，同步分支依赖             │
│  - 规则校验模块：分支命名、Commit规范、敏感数据校验         │
│  - 自动化执行模块：Git命令封装，支持自定义工作流与CI/CD触发 │
│  - 数据同步模块：TRAE-GitHub-WSL 2 状态与版本实时同步，支持通知机制 │
│  - 日志追溯模块：全流程日志记录，关联任务/PR/快照           │
├─────────────────────────────────────────────────────────────┤
│  优化功能层（Optimization Layer）                           │
│  - AI 代码评审模块：前置质量校验与修复建议                  │
│  - 敏感数据脱敏模块：识别、替换、加密存储敏感信息           │
│  - 多仓库联动模块：跨库依赖校验、版本对齐                  │
│  - 分支快照与回滚模块：快照创建、版本管理、一键回滚        │
│  - 智能冲突解决模块：提供冲突预警、自动合并建议及手动解决引导 │
│  - 工作流自动化模块：预设与自定义Git工作流，支持CI/CD触发   │
├─────────────────────────────────────────────────────────────┤
│  GitHub 联动层（GitHub Integration Layer）                  │
│  - MCP 协议适配模块：对接GitHub API，实现多库操作           │
│  - Issue/PR 管理模块：多库同步、分级PR关联与状态更新        │
│  - Webhook 双向同步模块：TRAE与GitHub实时数据互通，支持事件触发CI/CD │
├─────────────────────────────────────────────────────────────┤
│  数据层（Data Layer）                                       │
│  - 多GitHub仓库：存储各模块代码及分支版本                   │
│  - WSL 2 加密目录：存储敏感数据与配置，确保数据安全         │
│  - 快照仓库：存储分支历史快照，支持回滚                     │
│  - 日志数据库：存储操作日志、评审记录、敏捷任务数据         │
└─────────────────────────────────────────────────────────────┘
```

## 2. 核心模块依赖（保留原依赖+新增敏捷工具）

- **基础依赖**：TRAE 国际版 V2.0+、WSL 2 Ubuntu 24.04、Git 2.34+；

- **核心插件**：Git Agent、Multi-Repo Sync、Data Desensitization、Branch Snapshot；

- **敏捷插件**：Jira Connector、GitHub Projects Sync（TRAE 专属）；

- **环境依赖**：WSL 2 已配置 SSH 服务、GitHub 账号（多仓库管理员权限）、代码评审 AI API 密钥。

# 三、部署流程（WSL 2+敏捷适配+原核心配置）

## 1. 部署前提

- WSL 2 已启用，Ubuntu 24.04 分发版安装完成，网络可访问 GitHub 与 TRAE；

- TRAE 国际版为最新版，具备「项目管理员」权限；

- 已创建 GitHub 多仓库（frontend/backend/contract），获取对应 PAT（含 repo/issues 权限）；

- 敏捷工具（Jira/GitHub Projects）已部署，获取 API 密钥用于与 TRAE 联动。

## 2. 详细部署步骤（10步完成，含WSL 2与敏捷配置）

### 步骤1：WSL 2 Ubuntu 24.04 环境初始化

1. 更新系统并安装依赖：
        `sudo apt update && sudo apt install -y openssh-server git curl docker.io
sudo systemctl enable ssh --now  # SSH服务开机自启`

2. 配置固定IP（避免重启后变更）：
        在 Windows 中创建 `C:\Users\用户名\.wslconfig`，添加：`[wsl2]
networkingMode=bridged
vmSwitch=WSL2  # 提前在Hyper-V创建虚拟交换机`重启 WSL 2：`wsl --shutdown`，重新打开 Ubuntu 后执行 `ip addr` 记录固定IP（如192.168.1.100）。

3. 创建代码与加密目录：
        `mkdir -p ~/trae-projects/{frontend,backend,contract}  # 多仓库工作区
mkdir -p ~/.trae-secrets  # 敏感数据加密存储目录
chmod 700 ~/.trae-secrets  # 仅当前用户可访问`

### 步骤2：Windows 端口转发与防火墙配置

以管理员身份打开 PowerShell，执行：

```powershell

# 端口转发：Windows 2233端口→WSL 2 22端口
netsh interface portproxy add v4tov4 listenport=2233 listenaddress=0.0.0.0 connectport=22 connectaddress=192.168.1.100
# 开放防火墙端口
New-NetFirewallRule -DisplayName "TRAE-SSH-WSL2" -Direction Inbound -LocalPort 2233 -Protocol TCP -Action Allow
```

### 步骤3：TRAE 国际版 SSH 连接 WSL 2

1. 打开 TRAE 「远程开发」→「新建 SSH 连接」，填写：
        主机地址：Windows 局域网IP（如192.168.1.10）；

2. 端口：2233；

3. 用户名：Ubuntu 24.04 用户名（如ubuntu）；

4. 密码：Ubuntu 登录密码。

5. 点击「连接」，验证 TRAE 可访问 WSL 2 中的 `~/trae-projects` 目录。

### 步骤4：Git 与 GitHub 环境配置（WSL 2 中）

1. 配置 Git 全局信息：
        `git config --global user.name "GitHub用户名"
git config --global user.email "GitHub邮箱"
git config --global core.autocrlf false  # 避免换行符冲突`

2. 生成 SSH 密钥并关联 GitHub：
        `ssh-keygen -t ed25519 -C "GitHub邮箱"
cat ~/.ssh/id_ed25519.pub  # 复制公钥添加至GitHub→Settings→SSH Keys`验证连接：`ssh -T git@github.com`，显示「Hi 用户名!」即为成功。

3. 克隆多仓库至 WSL 2 工作区：
        `cd ~/trae-projects/frontend && git clone git@github.com:用户名/trae-frontend.git
cd ~/trae-projects/backend && git clone git@github.com:用户名/trae-backend.git
cd ~/trae-projects/contract && git clone git@github.com:用户名/trae-contract.git`

### 步骤5：Git Agent 核心插件安装

在 TRAE 插件市场搜索并安装：

- 核心插件：Git Agent、Multi-Repo Sync、Branch Snapshot；

- 安全插件：Data Desensitization；

- 敏捷插件：Jira Connector（或 GitHub Projects Sync）。

### 步骤6：敏捷工具联动配置

1. 打开 TRAE 「Git Agent」→「敏捷配置」，选择对应工具（如Jira）；

2. 输入 Jira 域名、API 密钥，完成授权；

3. 配置「任务-分支映射规则」：用户故事ID自动嵌入分支名（如feature/STORY-123-frontend-login）；

4. 启用「进度自动同步」：分支创建→任务「开发中」，PR合并→任务「已完成」。

### 步骤7：核心规则配置（分支+脱敏+评审）

1. **分支命名规则**（多模块模板化）：
        格式：`[类型]-[故事ID]-[模块标识]-[功能名]-[时间戳]`示例：`feature-STORY-123-frontend-login-20251220-14`、`hotfix-BUG-456-contract-reentrancy-20251220-16`

2. **敏感数据脱敏规则**：
        预设正则（可自定义）：私钥：`/0x[0-9a-fA-F]{64}/`；

3. API密钥：`/API_KEY\s*=\s*['"](.*?)['"]/`；

4. 替换规则：敏感数据→`{{类型_随机ID}}`（如{{PRIVATE_KEY_789}}），原始数据存至`~/.trae-secrets`。

5. **AI 评审规则**：
        勾选评审维度：代码规范、安全漏洞、性能优化、跨模块兼容性；评审阈值：通过率≥90%允许提交PR，＜90%则AI自动修复后重审。

### 步骤8：PR 分级合并规则配置

|流转路径|提交主体|校验条件|人工参与|
|---|---|---|---|
|研发分支→测试分支|AI Agent|评审通过、脱敏完成、关联任务|无|
|测试分支→预部署分支|AI Agent|测试通过率100%、跨库依赖满足|无|
|预部署分支→main分支|AI Agent提交，人工审核|预部署验证通过、版本对齐|2人审批，确认后合并|
### 步骤9：分支快照与回滚配置

1. 自动快照时机：分支创建、PR合并、代码每3次提交、任务状态变更；

2. 快照存储：`~/trae-projects/.snapshots/[仓库名]/[分支名]-[时间戳]`，保留90天；

3. 回滚权限：仅项目管理员可发起，回滚后自动更新任务状态为「回滚完成」。

### 步骤10：部署验证（全流程测试）

执行 TRAE 命令「Git Agent: 全流程验证」，自动完成：

1. 从 Jira 同步1条用户故事，生成多仓库 Issue；

2. 自动创建合规分支，AI 提交测试代码（含模拟敏感数据）；

3. 触发脱敏与评审，通过后提交分级 PR；

4. 模拟合并异常，执行一键回滚，验证快照有效性；

5. 生成验证报告，所有步骤显示「成功」则部署完成。

# 四、分支命名与提交规范（原规则+敏捷增强）

## 1. 分支命名终极规范

基础格式（优先级从左到右）：`[分支类型]-[敏捷标识]-[模块标识]-[功能描述]-[时间戳]`

|字段|取值范围|示例|必选|
|---|---|---|---|
|分支类型|feature/hotfix/release/main/develop|feature|是|
|敏捷标识|Jira故事ID/GitHub Issue号|STORY-123|是|
|模块标识|frontend/backend/contract|frontend|是|
|功能描述|英文/拼音（无空格）|login-adapt|是|
|时间戳|YYYYMMDD-HH|20251220-14|是|
合规示例：`hotfix-BUG-456-contract-reentrancy-20251220-16`、`release-v1.2.0-frontend-20251220-18`

## 2. Commit 提交规范

格式：`[类型]-[模块标识]: 描述信息（关联敏捷标识）`

- 类型：feat（新功能）、fix（修复）、docs（文档）、refactor（重构）、test（测试）；

- 示例：`feat-frontend: 完成登录页跨终端适配（关联STORY-123）`、`fix-contract: 修复重入攻击漏洞（关联BUG-456）`；

- TRAE 辅助：提交时自动生成模板，强制关联敏捷标识，不符合则拦截。

# 五、核心操作流程（原流程+敏捷任务链路）

## 1. 流程总览（敏捷驱动+多库协同）

全流程：**敏捷需求录入→多库Issue同步→Agent任务分配→多库分支开发→脱敏与评审→分级PR→合并快照→任务闭环→异常回滚**，分支流转：`develop→feature/*→test/*→staging/*→main`。

## 2. 详细操作步骤（11步闭环）

### 步骤1：敏捷需求录入与多库Issue同步

1. 在 Jira 中创建用户故事：「作为终端用户，希望登录Agent支持Android/iOS跨终端适配」，关联Sprint与优先级；

2. TRAE 自动同步该故事，Git Agent 解析后向 frontend/backend 仓库创建标准化Issue，标题格式：`[模块标识] 故事描述（STORY-123）`；

3. TRAE 看板生成聚合卡片，关联多库Issue与故事ID，状态为「待分配」。

### 步骤2：Agent任务分配与分支创建

1. 在 TRAE 看板将任务分配给「前端Agent组」与「后端Agent组」；

2. Git Agent 向对应Agent推送指令，自动基于develop分支创建合规分支：
        `# 前端分支
git checkout -b feature-STORY-123-frontend-login-adapt-20251220-14
# 后端分支
git checkout -b feature-STORY-123-backend-api-20251220-14`

3. TRAE 自动标记分支依赖（前端依赖后端API分支），看板显示「依赖待完成」。

### 步骤3：AI开发与代码提交

1. 后端Agent先完成API开发，提交代码：
        `git add .
git commit -m "feat-backend: 开发登录验证API（关联STORY-123）"
git push origin feature-STORY-123-backend-api-20251220-14`

2. TRAE 自动更新后端任务状态为「开发中」，解除前端分支依赖；

3. 前端Agent完成登录页适配开发，提交代码（含模拟API密钥）。

### 步骤4：敏感数据脱敏与AI评审

1. Git Agent 扫描前端代码，发现API密钥 `API_KEY = "sk-xxx"`，自动替换为 `API_KEY = "{{API_KEY_789}}"`，原始密钥加密存至 `~/.trae-secrets`；

2. 触发AI代码评审，发现前端代码存在跨终端适配问题，AI自动修复后重审，通过率达95%；

3. 脱敏与评审日志同步至 TRAE 与 Jira 故事评论区。

### 步骤5：分级PR提交与审核

1. AI 自动提交「研发→测试」PR，Git Agent 校验通过后合并至 test 分支，触发自动化测试；

2. 测试通过后，提交「测试→预部署」PR，合并至 staging 分支，Staging Agent 执行预部署验证；

3. 预部署通过后，提交「预部署→main」PR，TRAE 推送审核请求至2名管理员，审核意见实时同步至Jira。

### 步骤6：合并执行与快照创建

1. 人工审批通过后，Git Agent 执行合并（Squash and merge模式），自动删除feature分支；

2. 为 main 分支创建快照：`main-snapshot-20251220-16-STORY-123`，存储至快照仓库；

3. 同步更新 TRAE 看板与 Jira 故事状态为「已完成」，生成完成报告。

### 步骤7：异常回滚操作（一键触发）

1. 发现合并后登录功能异常，管理员在 TRAE 看板选择对应快照，发起「一键回滚」；

2. Git Agent 执行回滚：`git reset --hard 快照版本号`，自动删除异常PR记录；

3. 同步更新 Jira 故事状态为「回滚完成」，生成回滚日志，关联异常原因。

# 六、常见异常处理（原场景+敏捷与WSL 2专属）

|异常场景|处理流程|
|---|---|
|WSL 2 SSH 连接失败|1. 检查SSH服务：`sudo systemctl status ssh`；2. 验证端口转发：`netsh interface portproxy show v4tov4`；3. 重启WSL 2：`wsl --shutdown`|
|多库分支依赖阻塞|1. TRAE 标记阻塞分支，推送告警至依赖Agent；2. 依赖完成后自动解除阻塞；3. 延长Sprint周期（如需）并同步至Jira|
|敏感数据脱敏失败|1. 推送告警至TRAE，标注未脱敏字段；2. 人工补充正则规则；3. 重新执行脱敏并记录补录日志|
|敏捷任务状态同步异常|1. 检查TRAE与Jira API连接；2. 手动触发「状态同步」命令；3. 修复任务-分支映射规则|
|快照回滚失败|1. 检查快照文件完整性；2. 验证管理员权限；3. 手动执行回滚命令并补充日志|
# 七、权限管控矩阵（原矩阵+敏捷与WSL 2适配）

|操作主体|核心权限|禁止操作|
|---|---|---|
|TRAE Git Agent|1. 多库Issue/PR管理；2. 分支/快照/回滚操作；3. 敏捷任务同步；4. 脱敏与评审触发|1. 修改生产环境配置；2. 绕过人工合并main分支；3. 访问原始敏感数据|
|前端/后端Agent组|1. 对应模块分支开发；2. 合规提交代码；3. 查看关联任务与评审意见|1. 操作其他模块仓库；2. 提交未脱敏代码；3. 直接合并至main分支|
|项目管理员|1. 审核main分支PR；2. 配置规则与权限；3. 发起回滚指令；4. 调整敏捷任务|1. 直接推送代码至main；2. 删除未过期快照；3. 关闭脱敏功能|
|敏捷管理员|1. 录入用户故事与任务；2. 分配任务优先级；3. 查看全流程进度|1. 操作Git分支与PR；2. 修改开发规则；3. 访问代码与敏感数据|
# 八、附录（原工具包+敏捷与WSL 2专属）

## 1. 核心配置模板

- WSL 2 固定IP配置模板（.wslconfig）；

- 敏捷任务-分支映射规则模板（JSON）；

- 敏感数据脱敏规则模板（JSON）；

- PR 分级合并校验规则模板（YAML）。

## 2. 操作指令模板

- TRAE 敏捷任务同步指令：`Git Agent: 同步Jira任务 [故事ID]`；

- 快照创建指令：`Git Agent: 手动创建快照 [仓库名] [分支名] [备注]`；

- 回滚指令模板：`Git Agent: 回滚至快照 [快照ID] 原因：[异常描述]`。

## 3. 排查工具

- WSL 2 SSH 连接测试脚本；

- 多库依赖校验工具；

- 敏感数据扫描工具（手动触发版）；

- 敏捷任务同步校验工具。

# 结语

本说明书完整保留原 Git Agent 核心能力（多仓库联动、脱敏、快照回滚等），融入敏捷项目管理与多 Agent 协作理念，适配 WSL 2 Ubuntu 24.04 运行环境，形成「需求-开发-测试-部署-复盘」的全闭环方案。后续新增模块仓库或调整敏捷流程时，仅需通过 TRAE 配置界面扩展，Git Agent 将自动适配，无需重构核心逻辑。
> （注：文档部分内容可能由 AI 生成）