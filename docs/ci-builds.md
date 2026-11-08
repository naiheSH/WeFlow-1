# 自动构建安装包

推送 main 或在 Actions → Build installers → Run workflow 手动触发，生成以下六组产物，保留 30 天：

| 平台 | 架构 | 格式 |
| --- | --- | --- |
| macOS | x64 / arm64 | DMG、ZIP |
| Windows | x64 / arm64 | NSIS EXE |
| Linux | x64 / arm64 | AppImage、tar.gz |

每个架构使用对应原生 GitHub runner，以便安装正确架构的依赖。六个任务独立运行，失败不会取消其他架构。
推送 `v` 开头的版本标签（例如 `v5.0.1`）会同步安装包版本，在六组构建全部成功后发布到当前 fork 的 GitHub Releases。
日常构建从对应运行底部的 Artifacts 下载（需要登录 GitHub）。

安装包未配置商业签名和 macOS 公证。数据库及媒体插件不在源码中，需自行配置，见 [第三方组件说明](third-party-components.md)。
原生依赖能否支持特定平台仍取决于上游包；尤其 sherpa-onnx-node 当前未声明 Windows ARM64 后端，该架构的语音转写能力不能保证可用。
Windows ARM64 不打包仓库中 x64 专用的 VC runtime DLL，运行时使用系统对应的 VC runtime。
