# ReactOS Server Edition

> 基于 ReactOS AMD64 构建无桌面服务器版本（Server Console → Server Core）

## 目录结构

```
reactos-server/
├── reactos/              # upstream 源码（git clone）
├── patches/              # 裁剪补丁
├── config/               # 构建配置
├── scripts/              # 自动化脚本
├── output/               # 编译产物
└── README.md             # 本构建文档
```

---

# ReactOS Server Edition 构建文档 v0.1

## 1. 项目目标

### 项目名称

```
ReactOS Server Edition
```

### 第一阶段目标

基于 ReactOS AMD64 构建一个无桌面服务器版本。

目标：

- 支持 VMware/QEMU 虚拟机启动
- 支持命令行环境
- 支持基础网络
- 支持文件系统
- 支持 Windows NT 服务模型
- 为后续运行 Python/Go/Node/Java 做基础

---

# 2. 硬件环境要求

## 编译机

| 项目 | 最低 | 推荐 |
| --- | --- | --- |
| CPU | 4 核 | 8 核以上 |
| 内存 | 8GB | 16GB / 32GB |
| 硬盘 | 50GB | 100GB |
| 系统 | Windows 10/11 x64 | Windows Server 2022 |

---

# 3. 软件环境

## 操作系统

当前：

```
Windows Server 2022
```

## 编译工具

### Visual Studio

版本：

```
Visual Studio 2022 Community
```

安装：

```
Desktop development with C++
```

包含：

```
MSVC v143
Windows SDK
CMake Tools
```

### Git

检查：

```bat
git --version
```

### CMake

检查：

```bat
cmake --version
```

### Ninja

检查：

```bat
ninja --version
```

### Python

检查：

```bat
python --version
```

### Bison / Flex（MSYS2）

路径：

```
C:\msys64\usr\bin
```

包含：

```
bison.exe
flex.exe
```

加入 PATH：

```
C:\msys64\usr\bin
```

---

# 4. 获取源码

目录：

```
C:\Users\Administrator\reactos
```

浅克隆：

```bat
git clone --depth=1 https://github.com/reactos/reactos.git
```

目录结构：

```
reactos
├── base
├── dll
├── drivers
├── hal
├── ntoskrnl
├── sdk
├── subsystems
└── win32ss
```

---

# 5. 编译环境初始化

打开：

```
x64 Native Tools Command Prompt for VS 2022
```

进入：

```bat
cd /d C:\Users\Administrator\reactos
```

执行：

```bat
configure.cmd
```

生成：

```
output-VS-amd64
```

---

# 6. 编译

进入：

```bat
cd output-VS-amd64
```

执行：

```bat
ninja bootcd
```

输出：

```
bootcd.iso
```

---

# 7. 虚拟机测试环境

## VMware

配置：

```
CPU:  2 Core
RAM:  2048MB
Disk: 20GB
Network: Intel E1000
```

启动 ISO：

```
bootcd.iso
```

---

# 8. 第一阶段验收

系统启动：

```
ReactOS
C:\>
```

测试：

文件系统：

```
dir
copy
mkdir
```

网络：

```
ipconfig
ping
```

服务：

```
sc query
```

注册表：

```
reg query
```

---

# 第二阶段：Server 裁剪计划

## 目标架构

最终：

```
ReactOS Server Core
```

启动链：

```
BIOS / UEFI
   ↓
NT Kernel
   ↓
Service Manager
   ↓
CMD
   ↓
C:\>
```

## 裁剪原则

### 不删除（核心）

```
ntoskrnl
hal
kernel32
ntdll
advapi32
rpc
services
registry
drivers
tcpip
```

### 删除 / 禁用

桌面组件：

```
explorer.exe
shell
themes
desktop
screensaver
```

用户应用：

```
games
accessories
paint
notepad
calculator
```

GUI 管理：

```
control panel
settings
desktop applets
```

## Server 保留服务

系统服务：

```
Service Control Manager
RPC
Event Log
Registry
Network Stack
Plug and Play
```

## Server 启动目标

当前：

```
winlogon
   |
explorer
   |
desktop
```

改为：

```
winlogon
   |
cmd.exe
```

---

# 后续增加组件

## 网络管理

增加：

```
OpenSSH Server
HTTP Server
SMB
```

## Runtime（顺序）

1. Go
2. Python
3. Node.js
4. OpenJDK

---

# 下一步：ReactOS Server 裁剪 v0.1

顺序：

1. 找启动链
2. 禁用 Explorer
3. 默认进入 CMD
4. 删除应用组件
5. 缩减 ISO
6. 做 Server ISO

原则：第一刀不碰内核，只改

```
base/
modules/
win32ss 启动配置
```

先做出：

```
ReactOS Server Console
```

再逐步瘦身。
