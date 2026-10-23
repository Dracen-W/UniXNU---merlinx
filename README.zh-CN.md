<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo-light.svg">
    <img alt="UniXNU" src="assets/logo-light.svg" width="200">
  </picture>
</p>

<p align="center">
  <a href="https://unixnu.com"><img alt="文档" src="https://img.shields.io/badge/docs-unixnu.com-2A5BD7?logo=readthedocs&logoColor=white"></a>
  <a href="https://t.me/unixnu"><img alt="Telegram" src="https://img.shields.io/badge/Telegram-@unixnu-26A5E4?logo=telegram&logoColor=white"></a>
  <a href="https://qun.qq.com/universal-share/share?ac=1&authKey=Pubz23DOCY76nxjlGuSms2zesk4jRvas4zrXJRYk7omf7CprXbGQigLEI%2BCoYR8D&busi_data=eyJncm91cENvZGUiOiIxMTI2MTE2NzU5IiwidG9rZW4iOiJwSHJsUDN1UnN2Vi8yM210TjJRbzBIcERmcUpyOS9lb0hHVVpadWdvVFdScVpSYUFLT1YzTk5TWC9mN2xWYmtIIiwidWluIjoiMzE1MTEwOTc0MSJ9&data=_cJgr-jX9yJ2-GXwVjRN5FOhDyk5B-HAfIk5LSunbW_XkkmKqNXbpH33wzP93J0p9DI1fbjgBUrUfTVhwDcxjg&svctype=4&tempid=h5_group_info"><img alt="QQ 群" src="https://img.shields.io/badge/QQ%20Group-join-12B7F5?logo=qq&logoColor=white"></a>
  <a href="https://x.com/unixnu"><img alt="X" src="https://img.shields.io/badge/X-@unixnu-000000?logo=x&logoColor=white"></a>
</p>

<p align="center">
  <a href="README.md">English</a> | 简体中文
</p>

# UniXNU

🎉 **News:** 原生 Metal GPU 驱动已完全工作在小米平板 6S Pro（sheng）上。render server 通过 Adreno 740 合成系统界面，列入白名单的进程会获得各自的 Metal 设备。详见 [Adreno 740 驱动文档](https://unixnu.com/zh/drivers/native-adreno)。

一个独立项目，通过 UEFI 在非 Apple ARM64 硬件上运行 iOS 与 macOS。

## merlinx 移植

本仓库是由 [Dracen-W](https://github.com/Dracen-W) 维护的 Redmi Note 9（merlinx，MT6768/MT6769Z）独立移植。UniXNU 组织不维护这个 fork。UEFI 源码位于 [uefi-merlinx](https://github.com/Dracen-W/uefi-merlinx)。

| 组件 | 状态 |
| --- | --- |
| UEFI 固件 | `merlinxPkg` 可从源码构建，并从 p33 启动 |
| BootShim | 自覆盖问题已修复；320 字节 shim 会先把 copy/clean 代码块搬到安全地址，再加载固件卷 |
| 启动菜单 | 自定义 GOP 菜单可在屏幕显示；音量下滚动；音量上和电源键都能选中菜单项 |
| 启动镜像 | 64 MiB p33 镜像；用 `fastboot flash boot` 刷入 |
| 存储 | SdCardDxe 和 eMMC 初始化超时；Internal Storage 报 `EFI_NOT_FOUND`；USB 启动也失败 |
| UniXNU 和 XNU | 尚未到达；没有 ADT、负载或内核运行记录 |

`Mu_Basecore` 保持在上游提交。启动菜单补丁保存在 `uefi-merlinx`，并由 `board.toml` 引用；固件构建时会自动应用，构建结束后反向应用，因此新 clone 是自包含的。

按顺序的目标：

1. 修复 SdCardDxe 和 eMMC 超时，让 Internal Storage 枚举出来。
2. 让 USB 存储或 USB 启动可作为恢复路径。
3. 清理屏幕偏移、背景和 logo 交接。
4. 加入 ADT 和负载，进入 XNU 入口。
5. 从 XNU 入口推进到根挂载和 iOS 用户态。

设备说明见 [docs/zh/devices/merlinx.md](docs/zh/devices/merlinx.md)。这台设备没有 UART，且这个 bootloader 不支持 `fastboot boot`；测试镜像通过 `fastboot flash boot` 刷入 p33。

## 文档

文档位于 [unixnu.com](https://unixnu.com)，覆盖启动链、构建驱动、设备移植、驱动开发、XNU 4 KiB 页补丁、启动参数和设备说明。

- [总览](https://unixnu.com/zh/guide/overview)
- [构建](https://unixnu.com/zh/building)
- [启动镜像](https://unixnu.com/zh/guide/boot)
- [调试与日志](https://unixnu.com/zh/guide/debugging)
- [移植](https://unixnu.com/zh/porting/)
- [驱动](https://unixnu.com/zh/drivers/overview)
- [XNU 与 4 KiB 页](https://unixnu.com/zh/xnu/four-k-page-tables)
- [设备](https://unixnu.com/zh/devices/)

## 仓库

| 仓库 | 内容 |
| --- | --- |
| [UniXNU](https://github.com/UniXNU/UniXNU) | 构建驱动、UEFI 启动管理器、模块子模块和补丁 |
| [UniXNU-sheng](https://github.com/UniXNU/UniXNU-sheng) | 小米平板 6S Pro 的设备仓库 |
| [uefi-sheng](https://github.com/UniXNU/uefi-sheng) | 小米平板 6S Pro 的 UEFI 固件移植 |

驱动和 kit 是 UniXNU 组织下的独立公开仓库，以子模块形式引入。克隆时使用 `--recurse-submodules`，或运行 `git submodule update --init --recursive`。

设备仓库采用与本仓库相同的目录布局。同名模块会覆盖基础仓库的模块，设备仓库的 `package/` 在构建树中成为 kit。

## 参与贡献

阅读 [CONTRIBUTING.md](CONTRIBUTING.md)（英文），或查看[文档站的贡献页面](https://unixnu.com/zh/contributing/documentation)。

## 私有 blob

不要把 Apple IPSW、kernelcache、磁盘镜像、trust cache、Data 种子或厂商固件提交到本仓库。把它们放在本地缓存或外部存储。

## 许可证

BSD-2-Clause-Patent。见 [LICENSE](LICENSE)。
