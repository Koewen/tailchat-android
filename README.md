# Tailchat Android 自构建（CI 构建封装）

本仓库是一个 **GitHub Actions 构建封装**，用于从上游 [msgbyte/tailchat](https://github.com/msgbyte/tailchat) 自动编译 **Tailchat 安卓客户端 APK** 并发布到 Releases。

> 本仓库**不包含**上游源码，仅在 CI 运行时 `git checkout` 上游 `master` 分支并在隔离环境中完成构建。所有 Tailchat 相关代码、商标与知识产权均归上游作者所有，本项目仅提供可复现的构建流程与已编译产物。

---

## 📦 下载 APK

前往本仓库 **[Releases](../../releases)** 页面，下载最新版本的 `app-release.apk`（release 签名，约 23 MB）。

- 构建环境：React Native 0.71.2 / Android Gradle Plugin 7.3.1 / NDK 23.1.7779620 / Hermes / JDK 17 / Node 18
- 架构：包含 `armeabi-v7a` / `arm64-v8a` / `x86` / `x86_64` 通用包（universal）

---

## 🛠 自行构建（无需本地安卓环境）

任何人均可在 GitHub 上自行出包：

1. Fork 或克隆本仓库（已设为公开）。
2. 进入 **Actions → “Build Tailchat Android APK”** 工作流。
3. 点击 **Run workflow**（或向 `main` 分支 push）即可触发。
4. 构建完成后，在运行记录的 **Artifacts** 中下载 `tailchat-apk`，解压得到 `app-release.apk`。

CI 已自动处理以下上游移动端在公开 runner 上的构建障碍：

| 处理项 | 原因 |
| --- | --- |
| 手动安装 NDK `23.1.7779620` 等 SDK 包 | RN 0.71.2 写死 `ndkVersion`，且 runner 预装 SDK 与 `android-actions/setup-android` 冲突 |
| 剥离华为 AGConnect 推送依赖（classpath / plugin / `agconnect-core` / `hms:push` / 个推 hwp） | 华为 maven 仓库与 `agconnect-services.json` 在美国 runner 上不可达 |
| 生成 `debug.keystore` 并回填 `.env` 签名变量 | 上游 release 签名配置需有效 keystore，本地回退到生成的 debug 证书 |
| `assembleRelease` 失败时回退 `assembleDebug` | 双保险，确保至少产出可用 APK |

---

## 📱 安装与连接自托管实例

1. 将 `app-release.apk` 传到安卓设备，允许「未知来源」安装并安装。
2. 首次打开后，在 **设置 → 服务器地址** 中填入你的 Tailchat 实例地址，例如：
   - 公网：`https://<你的 Tailchat 域名>`（由你的反向代理对外暴露，端口依部署而定）
   - 内网：`http://<你的局域网 IP>:3000`
3. 使用网页端相同的账号登录即可。

> ⚠️ **关于主题**：本 APK 是上游原版 React Native 客户端，**不会**包含网页端的「玻璃流光」自定义主题（该主题为 web 插件，仅作用于浏览器/桌面端）。如需自定义外观，请使用网页客户端。

---

## 📄 许可证

- 上游 Tailchat 采用 **Apache License 2.0**，本仓库随附其 `LICENSE` 文本，构建产物依该许可证条款分发。
- 本仓库的 CI 工作流封装（`android.yml` 及相关说明）同样以 Apache-2.0 授权。

---

## ⚠️ 免责声明

- 本项目与 Tailchat 上游作者无隶属关系，仅供个人自托管用户方便地获取安卓客户端。
- 构建产物未经过第三方安全审计，请仅从本仓库 Releases 或自行构建获取，谨慎安装。
- 如上游许可证或商标政策有变更，以 upstream 为准。
