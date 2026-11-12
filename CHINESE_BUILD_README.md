# MesenCE 简体中文（Windows x64）试验补丁

本补丁基于用户提供的 MesenCE 源码快照，加入简体中文资源覆盖机制。英文资源仍是基准；未翻译的项目自动回退到英文，不会显示空白或内部资源 ID。

## 当前实现

- Windows 系统 UI 语言为中文时，启动时自动加载 `UI/Localization/resources.zh-CN.xml`。
- 可用环境变量 `MESEN_LANGUAGE=zh-CN` 强制中文；`MESEN_LANGUAGE=en` 强制英文。
- 中文资源作为外部 XML 文件复制到程序目录下的 `Localization` 子目录，无需重新编译即可继续编辑翻译。
- 本轮先翻译了常用菜单、部分设置页、常见弹窗和部分枚举。**这是一版汉化框架与首批词条，不是完整汉化**；调试器及部分冷门功能仍会显示英文，后续可继续补齐。

## Windows x64 构建

仓库自带的 `.github/workflows/build.yml` 已定义 Windows x64 构建流程。推荐将本补丁中的文件覆盖到你的 GitHub 仓库后推送，进入 GitHub 仓库的 **Actions** 页面，等待 `Build Mesen` 工作流完成，再下载名为 `Mesen (Windows - net10.0)` 或 `Mesen (Windows - net10.0 - AoT)` 的构建产物。

本地构建需要 Visual Studio 2022/2026（含 C++ 桌面开发工具）、Windows SDK、.NET 10 SDK，以及项目工作流下载的 Windows x64 `librashader.dll` 依赖。按仓库工作流执行：

```powershell
dotnet restore -p:TargetFramework=net10.0 -r win-x64 -p:PublishAot=false -p:BuildWithNetFrameworkHostedCompiler=true
msbuild -nologo -m -p:Configuration=Release -p:Platform=x64 -t:Clean,UI -p:TargetFramework=net10.0 -p:OptimizeUi=true
```

完整发布步骤请参考 `.github/workflows/build.yml` 和 `UI/Properties/PublishProfiles/Release.pubxml`。当前执行环境没有安装 .NET SDK / MSBuild，因此没有在此处产出或验证 Windows 可执行文件。

## 测试中文

在 Windows 中文系统上直接运行即可自动选择中文。若系统不是中文，可在 PowerShell 中运行：

```powershell
$env:MESEN_LANGUAGE = "zh-CN"
.\Mesen.exe
```

强制英文：

```powershell
$env:MESEN_LANGUAGE = "en"
.\Mesen.exe
```

如果你后续修改了 XML，只需保留 UTF-8 编码并重新启动程序。
