# 每日大赛 · HarmonyOS 客户端（分发仓库）

本仓库**只托管与分发已构建的安装包**，不包含源代码。

## 下载

- 最新版：见 [Releases](https://github.com/lujih/mrds-harmony/releases/latest)
- 固定文件名：`mrds-latest.hap`
- 校验：同 Release 的 `SHA256SUMS` / `version.json` 中的 `sha256`

## 安装

1. 用系统浏览器下载 HAP（微信内置浏览器会拦截）
2. 设置 → 安全 → 更多安全设置 → 允许安装未知来源应用
3. 文件管理打开 HAP 安装；覆盖安装会保留收藏、历史与设置
4. 校验：`sha256sum mrds-latest.hap`

## 更新

应用内「设置 → 检查更新」读取 `releases/latest/download/version.json`，比较 versionCode 后跳转下载页。

## 隐私

无账号体系；收藏、历史、搜索、播放进度、代理设置仅保存在本机。仅申请网络与网络状态权限。内容含成人向信息，仅面向 18 周岁以上用户。
