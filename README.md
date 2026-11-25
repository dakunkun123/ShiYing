# 拾影 Shiying — 抖音作品本地保存工具（Android）

> 粘贴抖音分享链接，即可在本地保存视频与图集。本地优先、无服务端、无账号体系，解析与下载均在设备上完成。

> **当前状态（v0.7.0 文档版）：** 本仓库当前包含 README、APK 说明（`APK.md`）与 MIT 许可证，应用源码随后补充。APK 二进制文件不随仓库提交，见 Releases；已验证的 APK 信息见 `APK.md`。

[![Platform](https://img.shields.io/badge/platform-Android%208.0%2B-green)](https://developer.android.com)
[![Language](https://img.shields.io/badge/language-Java-orange)](https://www.java.com)
[![Size](https://img.shields.io/badge/APK-90%20KB-blue)](#)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](#license)

## 功能

| 功能 | 说明 |
|---|---|
| 视频保存 | 解析作品后按画质档位保存，支持“最高可获取”画质；不把低清视频伪装成高清 |
| 图集保存 | 支持全选 / 逐张勾选，文件名按原作品顺序编号（如 `003`） |
| 实况照片 Motion Photo | 可导出为 `_MP.jpg`（JPEG 封面 + MP4 动态片段，含 Google Camera Motion Photo XMP），或导出为独立 MP4，或只存静态封面 |
| 多画质与多编码 | 读取 `play_addr` / `bit_rate` 多档位，优先 H.264 以提高兼容性；支持 HLS（AES-128 解密、TS / fMP4 合并） |
| 图片格式处理 | 支持 JPEG / PNG / WebP / AVIF / GIF，按需缩小大图（小图不放大） |
| 验证友好 | 自动检测验证码 / 登录弹窗，引导用户在抖音网页内自行完成验证；**不自动打码、不绕过验证** |
| 分享直达 | 支持从抖音“分享 → 拾影”直接接收 `text/plain` 链接 |
| 本地优先 | 无自有服务器，不上传作品链接与文件；下载地址仅用于本次下载 |

## 使用方法

1. 在抖音打开要保存的视频或图集，点击「分享 → 复制链接」。
2. 回到拾影，粘贴链接（整段分享文案可直接粘贴），点击「解析作品」。
3. 确认标题无误后，在「保存画质」中选择档位，点击「保存到相册」。
4. 图集：先「清空选择」再勾选需要的图片；实况作品出现「实况」标注后，再选择保存类型。
5. 保存位置：视频 `Movies/拾影`，图片 `Pictures/拾影`。相册未立即显示时，请在文件管理中查看。

> 详细操作见 `docs/manual.md`（随 APK 内置的使用指南）。

## 技术栈

- **语言 / 构建**：Java（原生 Android，无 Kotlin / Flutter / React Native 依赖），R8 Full 模式混淆发布
- **最低版本**：Android 8.0（API 26），单 `MainActivity`，代码界面（无 XML 布局文件）
- **解析**：`WebView` 加载抖音桌面端作品页，注入 `assets/extract.js` 与 `assets/challenge.js`
  - `extract.js`：从 `RENDER_DATA` / `__NEXT_DATA__` / `application/json` 脚本与 React Fiber（`__reactFiber$` / `__reactProps$`）中定位 `aweme_id` 对应的作品详情，提取 `video.play_addr`、`bit_rate`、`images`（`origin_image` / `display_image` / `download_url_list`）及实况 `video`
  - `challenge.js`：仅检测可见的验证码 / 登录弹窗与验证 iframe，不自动完成验证
- **下载**：`HttpURLConnection`（支持 `Range` / `Content-Range` 断点续传读取、`.part` 临时文件），HLS 解析 `#EXT-X-KEY` / `#EXT-X-BYTERANGE` / `#EXT-X-MAP`，`AES/CBC/PKCS5Padding` 解密
- **图片 / 实况**：`BitmapFactory` 按 `inSampleSize` 缩放；Motion Photo 按 Google Camera 规范合成 XMP（`Camera:MotionPhoto` / `MicroVideoOffset`），动态片段不重新编码
- **存储**：`MediaStore`（`DIRECTORY_MOVIES` / `DIRECTORY_PICTURES`），Android 10+ 采用分区存储，Android 8–9 需存储权限
- **第三方依赖**：无网络库 / 图片库 / 播放器依赖，仅使用 Android SDK 与 `org.json`；域名白名单仅限抖音 / 字节跳动 CDN（`douyin.com`、`iesdouyin.com`、`douyincdn.com`、`douyinpic.com`、`douyinvod.com`、`bytecdn.com` 等）

### 架构

```
分享链接 / 粘贴链接
  → WebView 加载 https://www.douyin.com/video|note|slides/<aweme_id>
  → extract.js 解析作品详情（标题 / 视频多档位 / 图集 / 实况）
  → challenge.js 检测验证 / 登录 → 用户自行完成
  → HttpURLConnection 下载（MP4 / HLS / 图片）
  → 本地处理（HLS 解密合并 / 图片缩放 / Motion Photo 合成）
  → MediaStore 写入 Movies/拾影 或 Pictures/拾影
```

## 项目结构（源码版）

```
app/
  src/main/
    AndroidManifest.xml          # package: local.shiying.app, 单 Activity, SEND text/plain 入口
    java/local/shiying/app/
      MainActivity.java          # 唯一 Activity：UI、解析调度、下载、存储
    assets/
      extract.js                 # 作品详情提取
      challenge.js               # 验证码 / 登录检测
      manual.txt                 # 内置使用指南
    res/mipmap-*/                # 启动图标
```

> 源码版的项目结构如下；当前仓库为文档版，源码随后补充，结构以源码上传后的实际内容为准。APK（`v0.7.0`，91,620 字节，SHA-256 见 `APK.md`）见 Releases。

## 构建

```bash
# Android Studio 打开本项目，或
./gradlew assembleRelease
# 产物：app/build/outputs/apk/release/app-release.apk
```

发布前请使用正式签名密钥重新签名：当前 APK 使用本地开发自签名证书（`CN=Shiying Local Development`），不适合作为正式发布签名。

## 局限与已知问题

- 抖音网页结构变化时解析可能失效，需同步更新 `extract.js` 中的选择器与字段名。
- 暂不支持断点续传；下载中请勿强制关闭应用。
- 部分作品因删除、无权限、地区限制或平台未提供资源而无法解析。
- 实况能否播放取决于系统相册；不支持时请改选「视频 · MP4」。
- HLS / 加密分片与高清档位以本次解析实际获取到的为准。

## 隐私

- 不收集账号、密码、通讯录与位置；不内置统计与广告 SDK。
- 不上传链接、标题与文件；Cookie 仅保留在设备 WebView 内，用于加载你已登录 / 已验证的抖音网页。

## 声明

本工具仅供学习与个人备份自己有权保存的作品使用。请遵守《抖音用户服务协议》与当地法律法规，尊重创作者版权，不得将下载内容用于商业传播、二次分发或侵犯他人权益。作者不对违规使用造成的后果负责。

## License

MIT License — 详见 `LICENSE`。
