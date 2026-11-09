# 每日大赛 · HarmonyOS 客户端（分发仓库）

本仓库**只托管已构建、已签名的安装包与更新元数据，不包含源代码**。
当前版本：**v1.6.9**（versionCode `1000020`）· 2026-11-06

- 包名：`com.mrds.app`
- 应用名：每日大赛
- 设备：phone / tablet
- 系统：HarmonyOS 6.1（API 23）及以上，target API 26

---

## 一、下载

| 用途 | 地址 |
| --- | --- |
| 最新安装包（固定名） | `https://github.com/lujih/mrds-harmony/releases/latest/download/mrds-latest.hap` |
| 最新版本页 | `https://github.com/lujih/mrds-harmony/releases/latest` |
| 全部历史版本 | `https://github.com/lujih/mrds-harmony/releases` |
| 更新元数据 | `https://github.com/lujih/mrds-harmony/releases/latest/download/version.json` |
| 校验和 | `https://github.com/lujih/mrds-harmony/releases/latest/download/SHA256SUMS` |
| 解析规则（可选） | `https://raw.githubusercontent.com/lujih/mrds-harmony/main/parser.json` |

新版 Release 内含 4 个资产（v1.6.9 起齐全；早期 Release 只有 HAP）：

```
mrds-<version>.hap   当前版本安装包（归档用，名字带版本号）
mrds-latest.hap      同一个文件，固定名，供 releases/latest 稳定链接
version.json         该版本的更新元数据（字段见第三节）
SHA256SUMS           安装包校验和（sha256sum 格式）
```

> **微信 / QQ 内置浏览器会拦截 `.hap` 下载**，请复制链接到系统浏览器（Chrome / Edge / 夸克 / UC）打开。
> 下载到的 HAP **不是双击就能装的安装包**：HarmonyOS NEXT 必须按第二节用 `hdc` / HoKit 侧载。

---

## 二、安装（HarmonyOS NEXT 侧载）

HarmonyOS NEXT **不支持在文件管理器里点开 HAP 直接安装**，也没有 Android 那种「允许安装未知来源应用」开关。HAP 只能通过 `hdc`（设备开开发者模式 + USB / 无线调试）装进去，或者用封装了 `hdc` 的图形化侧载工具。

### 1. 设备准备

1. 设置 → 关于本机 → 连续点击「版本号」7 次，开启开发者模式；
2. 设置 → 系统和更新 → 开发人员选项 → 打开「USB 调试」；
3. USB 数据线连接电脑，手机上弹出调试授权时点「允许」。

### 2. 选一个侧载工具

| 工具 | 平台 | 说明 |
| --- | --- | --- |
| [HoKit](https://github.com/yabi-zzh/HoKit) | Windows `.exe` · macOS `.dmg`（arm64 / x64）· Linux `.AppImage` | 内置 hdc 与 JRE；带设备注册、应用解析、应用重签名、文件管理、终端与日志，无需另装 SDK |
| DevEco Studio | Windows / macOS | 自带 hdc，可用 Run 直接安装 |
| 纯 hdc 命令行 | 任意 | SDK 里的 `hdc` 即可，适合脚本化 |

### 3. 安装

```bash
hdc list targets                    # 确认设备在线；无输出 = 未连接或未授权
hdc install -r mrds-latest.hap      # -r 覆盖安装，保留收藏/历史/设置
# 无线调试（先在手机上开启「无线调试」，或用 hdc tconn 配对）
hdc tconn 192.168.1.20:5555
hdc install -r mrds-latest.hap
```

HoKit 里的等价操作：「工具箱 → 终端」执行上面的命令；或用「文件管理」把 HAP 推到设备后再装。

### 4. 签名与设备注册（debug 包必看）

线上包是 **debug 签名**，只有 **UDID 已注册到该开发者账号** 的设备能装，其它设备会报签名校验失败（`signature verify failed` 一类错误）。

- HoKit「重签名 → 已注册设备」可查看并快速注册本机设备（开发者账号调试设备上限 100 台）；
- 设备不方便注册：用 HoKit「应用重签名」，以你自己账号下的调试证书 + Profile 重签，再安装重签后的包；
- **更换签名后覆盖安装会失败**（`install sign info inconsistent`），需先 `hdc uninstall com.mrds.app`；卸载会清除本机数据，且不可恢复；
- 面向公众分发需要改用 **release 签名**重新出包（`devecocli signature generate --type release` → `SIGNING_CONFIG=default_release`）。

---

## 三、校验

```bash
# 下载后校验（Windows 可用 certutil -hashfile mrds-latest.hap SHA256）
sha256sum mrds-latest.hap
cat SHA256SUMS
```

当前 v1.6.9 安装包：

```
文件     mrds-latest.hap
大小     1,328,493 字节（1.27 MB）
SHA-256  fbdb07acfcff82fa56f9a59ead3cceec269cafff7216a813fa25790dcc00a457
```

三处应完全一致：`SHA256SUMS` 文件、Release 页面资产摘要（GitHub `digest`）、仓库根 `version.json` 的 `sha256` 字段。

### version.json 字段

```json
{
  "versionCode": 1000020,
  "versionName": "1.6.9",
  "url": "https://ghproxy.net/https://github.com/lujih/mrds-harmony/releases/latest/download/mrds-latest.hap",
  "notes": "本次更新说明",
  "sha256": "fbdb07ac…a457",
  "sizeMb": "1.27 MB",
  "releasedAt": "2026-11-06"
}
```

| 字段 | 说明 |
| --- | --- |
| `versionCode` | 单调递增整数，客户端只比较它。`1.6.0 = 1000011`，每提一个小版本 +1（`1.6.9 = 1000020`） |
| `versionName` | 展示用版本号 |
| `url` | 安装包下载地址，必须 HTTPS 公网（客户端会拒绝 localhost / 内网段） |
| `notes` | 更新弹窗正文 |
| `sha256` / `sizeMb` / `releasedAt` | 可选；客户端有 `sha256` 时会在弹窗里显示前 16 位 |

---

## 四、应用内更新

「设置 → 检查更新」读取 `version.json`，与本地 bundle 的 `versionCode` 比较；只做提示与跳转下载，**不静默安装**（HarmonyOS 侧载必须由用户确认）。

客户端按下列顺序尝试，任一成功即返回（短超时，逐个回退）：

| # | 地址 | 说明 |
| --- | --- | --- |
| 1 | `ghproxy.net` + raw | 国内主用 |
| 2 | `releases/latest/download/version.json` | Release 资产镜像 |
| 3 | `raw.githubusercontent.com/…/main/version.json` | 直连 |
| 4 | `cdn.jsdelivr.net/gh/…@main/version.json` | CDN，命中旧缓存时最多滞后数小时 |
| 5 | GitLab 兜底 | 当前该地址没有 `version.json`，实际不可用（仅保留回退逻辑） |

仓库根目录的 `version.json` 是**发布真相源**，Release 里的同名资产是同一份副本，两者内容一致。

---

## 五、远程解析规则（parser.json）

站点改版后不必等新版本发布：客户端启动时读取仓库根目录的 `parser.json`，覆盖列表标题 / 封面 / 作者 / 日期、详情正文边界 / 图集属性 / 视频配置、搜索下一页等正则。

- 候选地址：ghproxy → raw → jsDelivr；
- 本地缓存 + 校验，**规则编译失败自动回退内置规则**，不会白屏；
- 设置页「更新解析规则」可手动刷新，Toast 显示版本与日期；
- 该文件与安装包版本无关，改规则不需要发版。

---

## 六、隐私与合规

- **无账号体系**：收藏、历史、搜索记录、播放进度、代理设置全部只存本机，不上传。
- **权限最小化**：`ohos.permission.INTERNET`、`ohos.permission.GET_NETWORK_INFO`（1.6.9 起新增 `ohos.permission.VIBRATE` 用于点按反馈）。不申请通讯录、相册、位置、麦克风、摄像头。
- **内容分级**：内容含成人向信息，仅面向 18 周岁以上用户，首次启动需确认。
- **版权**：客户端不托管任何内容，图文与视频版权归原站点及作者所有；本仓库仅分发客户端安装包。
- 数据删除：设置 → 清除历史 / 搜索记录 / 缓存，或直接卸载应用。

---

## 七、版本记录

| 版本 | 日期 | versionCode | 说明 |
| --- | --- | --- | --- |
| 1.6.9 | 2026-11-06 | 1000020 | 界面改版（首页/搜索/我的排版与字号统一）、图片按原比例展示并可点按重试、详情页振动反馈、站点自愈（远程解析规则 + 健康检查换线）、离线收藏（详情与图文本地化，断网可读） |
| 1.6.8 | 2026-11-04 | 1000019 | 废弃 API 迁移（getUIContext / getPromptAction）、详情图片滚动到底自动加载、分类合并展示 |

更早的 1.6.0–1.6.7 未在本仓库发布，仅保留在开发机的本地构建目录中。

---

## 八、仓库结构

```
README.md      本文件（下载与安装说明）
version.json   应用内更新元数据（发布真相源，随每次发版更新）
parser.json    远程解析规则（站点改版时热更新）
```

安装包与校验和只存在于 Releases，不占仓库体积，也不提供源码（`git clone` 只会得到这三个文件）。

---

## 九、维护者：发版流程

```bash
# 1) 改版本号：MrdsApp/AppScope/app.json5、SiteConfig.APP_VERSION、根 version.json 的 notes、CHANGELOG
# 2) 跑测试
npm test
# 3) 出包（默认 debug 签名；正式分发用 SIGNING_CONFIG=default_release）
devecocli build
node scripts/gen_dist.mjs MrdsApp/entry/build/default/outputs/default/entry-default-signed.hap \
  --base 'https://ghproxy.net/https://github.com/lujih/mrds-harmony/releases/latest/download'
# 4) 上传：先传资产，最后一步再写仓库根 version.json（顺序反了会有一段时间下载 404）
node scripts/publish_release.mjs --repo lujih/mrds-harmony --tag v<version> \
  --notes-file dist/release-notes.txt \
  --assets dist/mrds-<version>.hap dist/mrds-latest.hap dist/version.json dist/SHA256SUMS \
  --put-version version.json
# 5) 回归：五个更新地址都应返回新 versionCode，latest 安装包可下且哈希一致
```

检查表：版本号三处一致 → 测试通过 → `check arkts` 无 error → 构建成功 → 真机冒烟 → 隐私页可达 → SHA-256 已记录 → 更新检查可用。

---

## 十、常见问题

**下载不动 / 点下载跳到空白页**
`releases/latest/download/...` 会 302 到 `release-assets.githubusercontent.com`。部分网络只放行 `github.com` 而拦截该域名，表现为下载失败。改用代理或 `ghproxy.net` 前缀重试。

**点开 HAP 没有任何反应 / 没有安装界面**
这是正常现象。HarmonyOS NEXT 不提供「点开文件安装」，必须走 `hdc install`（或用 HoKit 的终端/文件管理）。见第二节。

**微信里点了没反应**
微信内置浏览器拦截 `.hap`。复制链接到系统浏览器下载，再用侧载工具安装。

**装不上，提示签名校验失败（`signature verify failed` / `9568xxx`）**
两种原因：① 当前包是 debug 签名，你这台设备的 UDID 没注册到对应开发者账号；② 设备上已有的同包名应用用的是另一套签名。解法：用 HoKit 重签名（用自己的调试证书）或先 `hdc uninstall com.mrds.app` 再装。

**应用内「去下载」下完装不上**
应用内只负责把 HAP 下到手机；安装仍需电脑侧载（HAP 可先用 HoKit 文件管理从手机取回，或直接在电脑下载）。

**检查更新说已是最新，但 Release 有新版**
客户端第 1 跳走 ghproxy，缓存通常几分钟；jsDelivr（第 4 跳）可能滞后数小时。以 `releases/latest` 与仓库根 `version.json` 为准，稍后重试。

**设置里「更新解析规则」报错**
远程规则被镜像改写或暂时不可达时会自动回退内置规则，不影响使用；重试或等下一版规则。

---

内容含成人向信息，仅面向 18 周岁以上用户。本仓库不存储、不代理任何站点内容。
