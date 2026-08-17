# AGENT_HANDOVER_LOG

- 创建时间：2026-07-05
- 最后更新：2026-08-10
- 项目：071801-桃子影视 (taozi-video)
- 分支：main
- 远端：https://github.com/Hippo1096/taozi-video.git
- 上游：https://github.com/FongMi/TV.git (branch: fongmi)

## 当前认知

- 基于 FongMi/TV 魔改的 Android TV 离线封装项目，品牌名"桃子影视"。
- 离线封装核心入口：`app/src/main/java/com/fongmi/android/tv/api/config/OfflineBootstrap.java`。
- 内置默认配置入口：`app/src/main/java/com/fongmi/android/tv/api/config/VodConfig.java`，使用 `assets://config.json`。
- TV/mobile 两套 UI，都包含双配置切换相关代码。

## 2026-08-10: FongMi/TV 上游同步（已于 2026-08-11 完整撤销）

- 备份分支：`backup-before-fongmi-update-0810`
- 上游 50 个新提交已拉取（fongmi 分支），本地与上游无共同 git 历史。
- **128 个文件曾被更新**（当时按相似度 >85% 判断为 base 文件覆盖）：
  - 包括 extractors、bean 类、API loader、DAO、server、utils 等
  - 主要 Java 源文件和 XML 布局文件
- **220 个新文件曾被添加**（上游新增功能）：
  - forcetech/hook/jianpian/thunder/tvbus/zlive 新模块
  - 播放器引擎重构（Exo/Mpv 分拆）
  - 音频/视频效果系统
  - 字幕搜索/API
  - 播放回放控制器（live/vod）
  - 新设置页面（解码、预加载、音频、字幕、视频）
- **1484 个自定义文件当时保留**（本地独有，未修改）
- **190 个冲突文件当时未处理**（见下方）

### 2026-08-11 根因复核与撤销

- GitHub Actions #15 在进入 Java 编译后出现重复类、缺失 MPV/Media3 类型、DAO 方法签名不一致等成组错误。
- 根因不是 SDK、缓存或单个依赖，而是提交 `c2ccbc8f` 只同步了 128 个相似文件和 220 个新增文件，却没有同步匹配的 `settings.gradle`、`app/build.gradle`、版本目录及 190 个冲突文件，形成跨版本混合源码树。
- “相似度 >85% 即可安全覆盖”不成立：源码、资源、构建脚本和模块依赖属于同一版本契约，不能按文件相似度拆开同步。
- 已用提交 `784e48e0` 完整撤销 `c2ccbc8f`，没有逐条修复编译错误；之后独立完成的桃子品牌图标、饭太硬双配置、43 个 EXT、本地直播源和业务迁移均保留。
- 上游同步今后只能采用完整版本迁移并一次性验证，或按明确功能垂直切片迁移；禁止再次按相似度批量覆盖源码。

### 需要手动合并/审查的文件（共 190 个）

**严重自定义（69 个，本地大幅魔改）：**
| 文件 | 说明 |
|------|------|
| `app/src/leanback/.../VideoActivity.java` | 213KB vs 50KB，视频播放主界面，大量自定义 |
| `app/src/main/.../PlayerManager.java` | 62KB vs 22KB，播放器管理器，完全自定义 |
| `app/src/main/.../Setting.java` | 44KB vs 3.9KB，设置项大幅扩展 |
| `app/src/main/.../DanmakuSearchDialog.java` | 36KB vs 5.5KB，弹幕搜索对话框 |
| `app/src/main/res/values/strings.xml` | 100KB vs 30KB，中文字符串 |
| `app/src/main/.../Updater.java` | 16KB vs 3KB，自定义更新逻辑 |
| `app/src/main/assets/css/ui.css` | 81KB vs 11KB，自定义 UI 样式 |
| 各种 launcher icon (PNG) | 桃子影视自定义图标 |
| 所有 `*strings.xml` | 中文翻译/自定义字符串 |

**边界情况（121 个，相似度在 50-85% 之间，需要逐文件审查）：**
- AndroidManifest.xml 文件（leanback/main/mobile）
- 大量 Activity/Fragment/Adapter（Home, Search, Setting, Live, Video 等）
- 布局 XML（activity_video, fragment_setting 等）
- build 配置（proguard-rules.pro）
- catvod 网络层（OkHttp, OkProxySelector, OkDns）

### 建议的合并策略

1. 不再把 128 个文件视为 safe；该批同步已经完整撤销。
2. 若未来重新同步上游，先建立完整构建契约和可回滚分支，再按以下优先级审查：
   - **高优先级**：App.java, Startup.java, AndroidManifest.xml（应用入口，上游可能有重要修复）
   - **中优先级**：HomeActivity, LiveActivity, VideoActivity（UI 相关，需手工 diff）
   - **低优先级**：strings.xml, styles.xml, colors.xml（UI 定制，保持本地）
   - **跳过**：custom logos/icons（肯定是桃子品牌，永不覆盖）

## 2026-08-10 晚：源/直播/指南落地，冲突先记录不盲改

### 已落地

- 远端仓库已改名：`Hippo1096/webhtv-offline` → `Hippo1096/taozi-video`
- 本地 origin 已切到：`https://github.com/Hippo1096/taozi-video.git`
- 上游 remote：`https://github.com/FongMi/TV.git`（分支 `fongmi`）
- 备份分支：`backup-before-fongmi-update-0810`
- 最新提交：`c3261818`（已 push；含交接日志）
- **业务操作/过程/指南已迁出本开源仓**，真源在工作区项目：
  - `D:/00HippoD/agent_workspace/081602-桃子影视源更新/`
  - 维护指南：`081002-源更新维护指南.md`
  - 饭太硬过程与原始缓存：`002过程/桃子源整理/`
- 本仓仅保留：源码、构建、`app/src/main/assets/*` 内置配置与直播兜底、技术 docs、指针文件 `docs/业务操作落点.md`

### 饭太硬源（assets 已写入本仓）

- 在线入口当前不可直取 JSON；拆分真源在业务项目过程目录
- 默认配置 `app/src/main/assets/config.json`：**79** 站点（无网盘源）
- 进阶配置 `app/src/main/assets/peach-advanced.json`：**86** 站点（含 7 个网盘源）
- 网盘源固定 7 个：`盘她/盘它/米搜/夸搜/盘搜/易搜/AList`

### 直播源

- `app/src/main/assets/list.txt` / `radio.txt` 本地兜底仍在本仓 assets

### 决策（用户确认）

- **先不盲目改 190 个 FongMi 冲突文件**
- 开源仓 ≠ 业务项目：过程与指南不进 003repos
- 自定义资产永不覆盖：`config.json`、`peach-advanced.json`、`spider.jar`、`peach_logo.png`、`list.txt`、`radio.txt`、OfflineBootstrap 及桃子品牌相关改动

## 待继续

- [x] GitHub Actions #16 验证撤销错误同步后的四 APK 构建；Release `v5.5.6-202608110828`
- [ ] 若未来重做 FongMi 同步，必须迁移完整版本契约，不按相似度批量覆盖
- [ ] 确认 OfflineBootstrap.java 与新 upstream 兼容
- [ ] 业务侧刷新饭太硬 JSON 后，再回写本仓 assets
