# APK 说明 — 拾影 Shiying v0.7.0

> 本文件说明已验证的 APK 构建信息。仓库当前版本为文档版：包含本说明与 README，应用源码随后补充。APK 二进制文件本身不随仓库提交，请通过 GitHub Releases 获取。

## 基本信息

| 项目 | 内容 |
|---|---|
| 应用名 | 拾影 Shiying |
| 文件名 | `___0.7.0_0_ow6m.apk` |
| 版本 | 0.7.0 |
| 包名 | `local.shiying.app` |
| 大小 | 91,620 字节（约 90 KB） |
| SHA-256 | `0cbda0a97403a35d4b21d1c911b87a66f083985520fbe66ed0e9c093b073d080` |
| 最低系统 | Android 8.0（API 26） |
| 入口 | 单 `MainActivity`，支持从抖音分享 `text/plain` 链接直达 |
| 权限 | `INTERNET`、`WRITE_EXTERNAL_STORAGE`（Android 8–9 首次保存时需要） |

核对方法：

```bash
sha256sum ___0.7.0_0_ow6m.apk
# 应输出 0cbda0a97403a35d4b21d1c911b87a66f083985520fbe66ed0e9c093b073d080
```

## 包内结构（已验证，共 20 个条目）

- `AndroidManifest.xml`、`resources.arsc`、`classes.dex`（69,348 字节，DEX 038，38 个类、576 个方法）
- `assets/challenge.js`（1,837 字节）、`assets/extract.js`（4,870 字节）、`assets/manual.txt`（5,241 字节，内置使用指南）
- `res/mipmap-*` 启动图标、`META-INF/MANIFEST.MF` / `SHIYING.RSA` / `SHIYING.SF`

无 Flutter / React Native 运行时，无第三方网络、图片或播放器库；仅使用 Android SDK 与 `org.json`。

## 签名

- 自签名开发证书：`CN=Shiying Local Development`
- 序列号：`7399023263551193551`（`0x66ae9c74dcd201cf`）
- 有效期：2026-10-02 至 2054-02-17
- 正式发布前请使用正式签名密钥重新签名，勿用此开发证书发布。

## 安装与使用

1. 在手机上允许安装未知来源应用后安装 APK。
2. 在抖音打开作品，点击「分享 → 复制链接」，回到拾影粘贴并点击「解析作品」。
3. 确认标题后选择画质，点击「保存到相册」。
4. 保存位置：视频 `Movies/拾影`，图片 `Pictures/拾影`。

详细步骤见 `README.md` 与 APK 内置的 `assets/manual.txt`。

## 能力与限制（摘要）

- 可保存视频多画质、图集单张 / 全选、实况照片（Motion Photo / MP4 / 静态封面）。
- 支持 HLS（AES-128 解密、TS / fMP4 合并）与 JPEG / PNG / WebP / AVIF / GIF。
- 验证码 / 登录由用户在抖音网页内自行完成，软件不自动打码、不绕过验证。
- 暂不支持断点续传；抖音网页结构变化时解析可能失效。
- 仅供学习与个人备份自己有权保存的作品使用，请尊重创作者版权与平台规则。
