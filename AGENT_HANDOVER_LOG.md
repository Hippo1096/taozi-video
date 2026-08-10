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

## 2026-08-10: FongMi/TV 上游同步

- 备份分支：`backup-before-fongmi-update-0810`
- 上游 50 个新提交已拉取（fongmi 分支），本地与上游无共同 git 历史。
- **128 个文件已更新**（相似度 >85% 的 base 文件，安全覆盖）：
  - 包括 extractors、bean 类、API loader、DAO、server、utils 等
  - 主要 Java 源文件和 XML 布局文件
- **220 个新文件已添加**（上游新增功能）：
  - forcetech/hook/jianpian/thunder/tvbus/zlive 新模块
  - 播放器引擎重构（Exo/Mpv 分拆）
  - 音频/视频效果系统
  - 字幕搜索/API
  - 播放回放控制器（live/vod）
  - 新设置页面（解码、预加载、音频、字幕、视频）
- **1484 个自定义文件已保留**（本地独有，未修改）
- **190 个冲突文件需要手动审查**（见下方）

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

1. 对 128 个已更新的 safe 文件，检查编译是否通过
2. 对 190 个冲突文件，优先级：
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
- 最新提交：`31a356b5`（已 push）
- 维护指南：`docs/源更新维护指南.md`（FongMi 更新、饭太硬拆分、直播内置、坑点）

### 饭太硬源

- 在线入口当前不可直取 JSON：`http://饭太硬.top/tv`、`http://www.饭太硬.cc/tv` 等返回 HTML/图片混淆
- 本次以缓存真源 `002过程/桃子源整理/001原始/config-饭太硬-原始.json` 重新拆分：
  - 默认配置 `app/src/main/assets/config.json`：**79** 站点（无网盘源）
  - 进阶配置 `app/src/main/assets/peach-advanced.json`：**86** 站点（含 7 个网盘源）
- 网盘源固定 7 个：`盘她/盘它/米搜/夸搜/盘搜/易搜/AList`
- 后续若 App 内「点播源设置 → 转换」拿到新 JSON，覆盖原始缓存后再跑指南中的拆分脚本

### 直播源

- 新增本地兜底：
  - `app/src/main/assets/list.txt`（BurningC4/Chinese-IPTV IPv4）
  - `app/src/main/assets/radio.txt`（基础电台）
- 配置内 `lives` 仍保留多路在线备用；本地 `./list.txt` 作首路兜底

### 决策（用户确认）

- **先不盲目改 190 个 FongMi 冲突文件**
- 冲突只记录在本日志；下一步优先编译验证，再按失败点最小修复
- 自定义资产永不覆盖：`config.json`、`peach-advanced.json`、`spider.jar`、`peach_logo.png`、`list.txt`、`radio.txt`、OfflineBootstrap 及桃子品牌相关改动

## 待继续

- [ ] 编译验证：确认 128 更新 + 220 新增后的可构建性
- [ ] 仅对编译失败相关冲突做最小修复；不整批盲合 190 文件
- [ ] 确认 OfflineBootstrap.java 与新 upstream 兼容
- [ ] 若拿到最新饭太硬真实 JSON，再刷新双配置
