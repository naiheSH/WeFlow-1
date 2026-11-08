# 自动构建安装包

推送 main 或在 Actions → Build installers → Run workflow 手动触发，生成以下六组产物，保留 30 天：

| 平台 | 架构 | 格式 |
| --- | --- | --- |
| macOS | x64 / arm64 | DMG |
| Windows | x64 / arm64 | NSIS EXE |
| Linux | x64 / arm64 | AppImage |

每个架构使用对应原生 GitHub runner，以便安装正确架构的依赖。六个任务独立运行，失败不会取消其他架构。
推送 `v` 开头的版本标签（例如 `v5.0.1`）会同步安装包版本，在六组构建全部成功后发布到当前 fork 的 GitHub Releases。
日常 main 构建或手动运行成功后，会创建 `ci-<run_id>-<attempt>` 预发布，直接从 Releases 的 Assets 下载对应平台、架构的单个安装包，无需解压 ZIP。
macOS 只生成 DMG，Windows 只生成 EXE，Linux 只生成 AppImage。GitHub 自动附带的 Source code ZIP/tar.gz 是源码，不是安装包。
Actions Artifacts 仍保留作 CI 中转和备份；GitHub 会自动把 Artifact 封装成 ZIP，正常下载请使用 Releases。

安装包未配置商业签名和 macOS 公证。数据库及媒体插件不在源码中，需自行配置，见 [第三方组件说明](third-party-components.md)。
原生依赖能否支持特定平台仍取决于上游包；尤其 sherpa-onnx-node 当前未声明 Windows ARM64 后端，该架构的语音转写能力不能保证可用。
Windows ARM64 不打包仓库中 x64 专用的 VC runtime DLL，运行时使用系统对应的 VC runtime。

Windows ARM64 的 Electron/Node 使用原生 ARM64；ffmpeg-static 未提供 Windows ARM64 二进制，因此附带独立 x64 FFmpeg，通过 Windows 11 的 x64 模拟运行。
