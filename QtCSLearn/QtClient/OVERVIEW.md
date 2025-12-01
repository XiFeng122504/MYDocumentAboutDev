# QtClient 项目学习总览

> 这是一个**学习型项目**，目标是通过开发一个完整的 Qt 客户端，深入理解网络编程、Qt 框架和 C++ 实践。

---

## 🎯 项目概述

### 项目组成
本项目包含两个部分：

1. **VimUsing 服务器**（位于 `VimUsing/`）
   - 基于 Linux Epoll 的高性能 TCP 服务器
   - 功能：用户登录、注册、文件上传、Echo 回声测试
   - 技术：C++、Epoll、非阻塞 I/O、边缘触发（ET）
   - 状态：**已完成**，可作为参考

2. **Qt 客户端**（项目根目录）
   - 基于 Qt5 的跨平台客户端程序
   - 功能：连接服务器、用户认证、文件上传、消息通信
   - 技术：Qt 5.12、C++17、CMake
   - 状态：**开发中**（当前进度 60%）

### 核心目标
- ✅ **主要目标**：带学习者从零开始构建一个完整的网络客户端
- ✅ **学习方法**：理解原理 → 编写代码 → 测试验证 → 解决问题
- ✅ **重点掌握**：Qt 网络编程、二进制协议、异步通信、状态管理

---

## 📊 当前进度追踪

### 进度总览

| 阶段 | 内容 | 权重 | 完成度 | 贡献 |
|------|------|------|--------|------|
| **第一阶段** | 项目准备（CMake、环境配置） | 10% | 100% | 10% |
| **第二阶段** | Protocol层（消息编解码） | 15% | 100% | 15% |
| **第三阶段** | Network层（NetworkClient） | 15% | 100% | 15% |
| **第四阶段** | Services层（业务逻辑封装） | 20% | 100% | 20% |
| **第五阶段** | UI层（界面设计与实现） | 30% | 0% | 0% |
| **第六阶段** | 测试优化（端到端测试） | 10% | 0% | 0% |
| **总计** | | **100%** | | **60%** |

**最后更新：** 2025-11-22

---

### 已完成 ✅

#### 第一阶段：项目准备（100%）
- [x] 分析 VimUsing 服务器协议
- [x] 设计客户端架构（分层设计）
- [x] 搭建 CMake 构建系统
- [x] 解决 Qt 环境配置问题（ARM64 vs x86_64）
- [x] 创建项目目录结构

#### 第二阶段：协议层实现（100%）✅
- [x] 理解 QByteArray 基础用法
- [x] 学习多字节整数的编解码（quint32）
- [x] 掌握 QDataStream 和字节序转换
- [x] 编写测试代码验证理解（`draft/test_*.cpp`）
- [x] 定义 Protocol.h 接口
- [x] 实现 `Protocol::encodeMessage()`
- [x] 实现 `Protocol::decodeMessage()`（处理粘包/分包）
- [x] 实现 `Protocol::createLoginMessage()` 和 `getPayloadAsString()`
- [x] 编写单元测试验证编解码正确性

#### 第三阶段：网络层实现（100%）✅
- [x] 学习 QTcpSocket 基础用法（`draft/test_socket.cpp`）
- [x] 理解 Qt 信号槽机制
- [x] 创建简单的测试服务器（`draft/test_server.cpp`）
- [x] 设计 NetworkClient 接口
- [x] 实现 NetworkClient.cpp
  - [x] 构造函数（连接信号槽）
  - [x] connectToServer/disconnectFromServer
  - [x] sendMessage（Protocol 编码 + socket 发送）
  - [x] onSocketReadyRead（处理 TCP 粘包/分包）
- [x] 编写测试验证收发功能（`draft/test_network_client.cpp`）

#### 第四阶段：业务层（67%完成）✅ 部分完成
- [x] 实现 `EchoService` 回声测试服务
  - [x] 理解 Service 层的职责（封装、转换、隔离）
  - [x] 学习类关系设计（依赖、聚合、组合、继承）
  - [x] 设计 EchoService 接口（隐藏 Message 细节）
  - [x] 实现数据转换（QString ↔ Message）
  - [x] 编写测试验证（test_EchoService.cpp）
- [x] 实现 `AuthService` 登录服务
  - [x] 理解登录协议（LoginRequest → Ack/Error）
  - [x] 学习多信号设计（loginSuccess + loginFailed）
  - [x] 处理两种响应类型（成功/失败）
  - [x] 理解职责划分（服务器安全 vs 客户端传递）
  - [x] 编写测试验证协议兼容性（test_AuthService.cpp）
- [x] 实现 `FileUploadService` 文件上传服务 ✅ 已完成
  - [x] 分析文件上传协议（Begin → Chunk → End）
  - [x] 设计 FileUploadService 接口（4 个信号设计）
  - [x] 理解分块上传机制（64KB 分块）
  - [x] 设计状态管理方案（状态机）
  - [x] 实现 FileUploadService.cpp（构造函数、uploadFile、状态机）
  - [x] 实现辅助函数（sendBegin、sendNextChunk、sendEnd、cleanUp）
  - [x] 实现防御性编程（资源清理、错误处理）
  - [x] 编写测试程序验证（test_FileUploadService.cpp）

### 进行中 🔄

**当前焦点**：Services层已全部完成！准备开始UI层开发

**下一步任务**：
1. 设计 UI 层架构（LoginWindow + MainWindow）
2. 学习 Qt Widgets 基础知识
3. 实现登录界面（LoginWindow）
4. 实现主窗口（MainWindow）
5. 集成 Services 层到 UI

### 待完成 📋

#### 第五阶段：UI层（未开始）
- [ ] 设计登录界面
- [ ] 设计主界面（文件上传、消息显示）
- [ ] 连接 UI 和业务逻辑

#### 第六阶段：测试与优化（未开始）
- [ ] 端到端测试（连接真实服务器）
- [ ] 性能优化
- [ ] 错误处理完善

---

## 🧠 AI 助手指导方针

> **重要**：以下是 AI 助手在帮助学习者时应遵循的原则。

### 核心原则

1. **教学导向，而非代码生成** ⭐ **最重要**
   - ✅ 解释原理 → 提供示例 → 引导实践
   - ✅ 给关键提示、思路、步骤
   - ✅ 指出错误、解释为什么错
   - ❌ **除非学习者明确要求，否则绝不直接给完整代码**
   - ❌ 不要一次性写完整个函数
   - 正确做法：分步骤引导，每步让学习者自己动手

2. **渐进式学习**
   - 从简单概念开始（如先理解 `quint8`，再学 `quint32`）
   - 每次只引入一个新知识点
   - 确保前一个知识点理解后再继续

3. **实践验证**
   - 鼓励编写小测试程序（放在 `draft/` 目录）
   - 对比正确与错误的写法
   - 通过实际运行结果加深理解

4. **问题驱动**
   - 先提出问题："为什么 `append()` 不能用于 quint32？"
   - 再解释原理
   - 最后给出解决方案

5. **记录学习过程**
   - 每次学习结束后，更新 `client/claude.md`
   - 记录关键知识点、踩坑经历、解决方案
   - 压缩已完成主题，保留精华

### 具体做法

#### 当学习者遇到困难时
```
1. 先确认困惑点
2. 回顾相关基础知识
3. 提供简化示例（最好在 draft/ 中编写测试）
4. 引导学习者自己动手实现
5. 提供反馈和修正
```

#### 当开始新主题时
```
1. 检查 claude.md 是否已有记录
2. 说明学习目标和预期成果
3. 列出需要掌握的核心概念
4. 分步骤引导实现
5. 在 claude.md 中记录学习内容
```

#### 当完成一个模块时
```
1. 总结核心要点
2. 压缩 claude.md 中的详细过程
3. 更新 OVERVIEW.md 的进度
4. 预告下一步学习内容
```

### 推荐学习路径

**对于当前阶段（Protocol 实现）**：
1. 先在 `draft/` 中编写独立测试，验证理解
2. 理解正确后，再实现到 `core/Protocol.cpp`
3. 编写单元测试确保实现正确
4. 与服务器代码对照（`VimUsing/src/protocol/Message.cpp`）

**对于未来阶段**：
- 网络层：先学习 QTcpSocket 基础，再实现 NetworkClient
- 业务层：先实现 Echo（最简单），再做登录和文件上传
- UI层：使用 Qt Designer，降低 UI 开发复杂度

---

## 🏗️ 项目文件结构规范

> **重要**：这是项目的固定架构，任何 Claude 会话都必须遵循此结构，不得随意修改。

### 完整目录结构

```
QtClient/                       # 项目根目录
├── CMakeLists.txt              # 构建配置（已完成）
├── main.cpp                    # 程序入口（已完成）
├── docs/                       # 文档目录（git submodule）
│   └── QtCSLearn/QtClient/
│       ├── AI-GUIDE.md        # AI助手指导
│       ├── LEARNING-LOG.md    # 详细学习日志
│       └── OVERVIEW.md        # 项目总览
│
├── core/                       # 核心层：协议和网络
│   ├── Protocol.h             # 协议接口定义（已完成）
│   ├── Protocol.cpp           # 协议实现（已完成）
│   ├── NetworkClient.h        # 网络客户端接口（已完成）
│   └── NetworkClient.cpp      # 网络客户端实现（已完成）
│
├── services/                   # 业务逻辑层
│   ├── AuthService.h          # 认证服务接口（已完成）
│   ├── AuthService.cpp        # 认证服务实现（已完成）
│   ├── FileUploadService.h    # 文件上传服务接口（已完成）
│   ├── FileUploadService.cpp  # 文件上传实现（已完成）
│   ├── EchoService.h          # Echo服务接口（已完成）
│   └── EchoService.cpp        # Echo服务实现（已完成）
│
├── models/                     # 数据模型层
│   ├── User.h                 # 用户模型（待创建）
│   └── UploadTask.h           # 上传任务模型（待创建）
│
├── ui/                         # 用户界面层
│   ├── LoginWindow.h          # 登录窗口（待创建）
│   ├── LoginWindow.cpp        # 登录窗口实现（待创建）
│   ├── LoginWindow.ui         # 登录窗口UI（待创建）
│   ├── MainWindow.h           # 主窗口（待创建）
│   ├── MainWindow.cpp         # 主窗口实现（待创建）
│   └── MainWindow.ui          # 主窗口UI（待创建）
│
└── draft/                      # 测试和学习代码（临时）
    ├── CMakeLists.txt         # 测试构建配置
    ├── test_protocol.cpp      # 协议编码测试
    ├── test_decode.cpp        # 协议解码测试
    ├── test_socket.cpp        # Socket测试
    ├── test_network_client.cpp # NetworkClient测试
    ├── test_EchoService.cpp   # EchoService测试
    ├── test_AuthService.cpp   # AuthService测试
    └── test_FileUploadService.cpp  # FileUploadService测试
```

### 核心模块详细说明

#### 1️⃣ Core 层（核心层）

**Protocol.h / Protocol.cpp**
- **职责**：消息的编码和解码
- **接口**：
  ```cpp
  static QByteArray encodeMessage(const Message& message);
  static bool decodeMessage(QByteArray& buffer, Message& outMessage);
  static Message createLoginMessage(const QString& username, const QString& password);
  static QString getPayloadAsString(const Message& message);
  ```
- **依赖**：Qt Core (QByteArray, QDataStream)
- **状态**：✅ 已完成

**NetworkClient.h / NetworkClient.cpp**
- **职责**：管理TCP连接、发送接收数据
- **接口**（预计）：
  ```cpp
  class NetworkClient : public QObject {
      void connectToServer(const QString& host, quint16 port);
      void sendMessage(const Message& message);
      void disconnectFromServer();

      // 信号
      signals:
          void connected();
          void disconnected();
          void messageReceived(const Message& message);
          void errorOccurred(const QString& error);
  };
  ```
- **依赖**：Qt Network (QTcpSocket), Protocol
- **状态**：✅ 已完成

#### 2️⃣ Services 层（业务逻辑层）

**AuthService.h / AuthService.cpp**
- **职责**：处理登录、注册逻辑
- **接口**（预计）：
  ```cpp
  class AuthService : public QObject {
      void login(const QString& username, const QString& password);
      void logout();

      signals:
          void loginSuccess();
          void loginFailed(const QString& reason);
  };
  ```
- **依赖**：NetworkClient, Protocol
- **状态**：✅ 已完成

**FileUploadService.h / FileUploadService.cpp**
- **职责**：处理文件上传逻辑（分块、进度）
- **依赖**：NetworkClient, Protocol
- **状态**：✅ 已完成

**EchoService.h / EchoService.cpp**
- **职责**：处理Echo测试（最简单，建议先实现）
- **依赖**：NetworkClient, Protocol
- **状态**：✅ 已完成

#### 3️⃣ Models 层（数据模型层）

**User.h**
- **职责**：用户信息模型
- **内容**：用户名、登录状态等
- **状态**：📋 待创建

**UploadTask.h**
- **职责**：上传任务模型
- **内容**：文件名、大小、进度等
- **状态**：📋 待创建

#### 4️⃣ UI 层（用户界面层）

**LoginWindow.h / .cpp / .ui**
- **职责**：登录界面
- **状态**：📋 待创建

**MainWindow.h / .cpp / .ui**
- **职责**：主窗口（文件上传、消息显示）
- **状态**：📋 待创建

### 开发顺序规范

**阶段1：Core层** ✅ **已完成**
1. ✅ Protocol.h（已完成）
2. ✅ Protocol.cpp::encodeMessage()（已完成）
3. ✅ Protocol.cpp::decodeMessage()（已完成）
4. ✅ Protocol.cpp::辅助函数（已完成）
5. ✅ NetworkClient.h + .cpp（已完成）

**阶段2：Services层** ✅ **已完成**
1. ✅ EchoService（已完成）
2. ✅ AuthService（已完成）
3. ✅ FileUploadService（已完成）

**阶段3：UI层** ⬅️ **当前阶段**
1. 📋 LoginWindow（下一步）
2. 📋 MainWindow

**阶段4：集成测试**

### 文件创建规则

1. **头文件和实现文件必须配对**
   - 有 `.h` 必须有对应的 `.cpp`（除了纯模板类）

2. **文件位置固定**
   - Core层 → `core/`
   - Services层 → `services/`
   - Models层 → `models/`
   - UI层 → `ui/`
   - 测试代码 → `draft/`
   - 文档 → `docs/` (git submodule)

3. **命名规范**
   - 类名：大驼峰（如 `NetworkClient`）
   - 文件名：与类名一致（如 `NetworkClient.h`）
   - 函数名：小驼峰（如 `encodeMessage`）

4. **依赖规则**
   - UI层 → Services层 → Core层
   - 不允许反向依赖
   - Core层不依赖其他层

---

## 📁 重要文件索引

### 客户端关键文件

| 文件路径 | 作用 | 状态 |
|---------|------|------|
| `core/Protocol.h` | 协议层接口定义 | ✅ 已完成 |
| `core/Protocol.cpp` | 协议层实现 | ✅ 已完成 |
| `core/NetworkClient.h` | 网络客户端接口 | ✅ 已完成 |
| `core/NetworkClient.cpp` | 网络客户端实现 | ✅ 已完成 |
| `services/` | 业务逻辑层目录 | ✅ 已完成 |
| `docs/QtCSLearn/QtClient/` | 学习文档目录 | 持续更新 |
| `draft/` | 测试代码目录 | 持续添加 |
| `CMakeLists.txt` | 构建配置 | ✅ 已完成 |
| `main.cpp` | 程序入口 | ✅ 已完成 |

### 服务器参考文件

服务器已部署到 Linux 服务器上，协议规范请参考本文档中的协议规范章节。

### 学习文档

| 文件 | 内容 |
|-----|------|
| `docs/QtCSLearn/QtClient/OVERVIEW.md` | 项目总览和AI指导（本文档）|
| `docs/QtCSLearn/QtClient/LEARNING-LOG.md` | 详细学习记录和知识点 |
| `docs/QtCSLearn/QtClient/AI-GUIDE.md` | AI助手指导文档 |
| `README.md` | 项目说明和构建步骤 |

---

## 🔌 协议规范

### 消息格式
```
[1字节: 消息类型] [4字节: Payload长度] [N字节: Payload数据]
```

- **字节序**：大端（BigEndian，网络标准）
- **消息类型**：`quint8` 枚举
- **长度字段**：`quint32`，表示 Payload 的字节数
- **Payload**：可变长度的二进制数据

### 消息类型定义

| 类型值 | 名称 | 方向 | 说明 |
|-------|------|------|------|
| 1 | LoginRequest | C→S | 登录请求（格式：`username:password`）|
| 2 | EchoRequest | C→S | Echo测试 |
| 3 | FileUploadBegin | C→S | 开始上传（包含文件名） |
| 4 | FileUploadChunk | C→S | 文件数据块 |
| 5 | FileUploadEnd | C→S | 完成上传 |
| 100 | Ack | S→C | 成功响应 |
| 101 | Error | S→C | 错误响应 |

### 示例

**登录消息编码**：
```
用户名：alice
密码：password123
Payload：alice:password123 (18字节)

编码结果（16进制）：
01                          // 类型 = LoginRequest
00 00 00 12                 // 长度 = 18
61 6C 69 63 65 3A ...       // "alice:password123"
```

---

## 🛠️ 技术栈

### 客户端
- **语言**：C++17
- **框架**：Qt 5.12.12
- **构建**：CMake 3.10+
- **平台**：macOS (ARM64/x86_64)

### 服务器（已完成，供参考）
- **语言**：C++14
- **核心技术**：Linux Epoll、非阻塞I/O、边缘触发
- **构建**：CMake
- **平台**：Linux

### 开发工具
- **IDE**：VSCode
- **调试**：Qt Creator / GDB / lldb
- **测试**：手动测试 + 未来可能加入 Qt Test

---

## 📖 学习资源

### Qt 官方文档
- QByteArray: https://doc.qt.io/qt-5/qbytearray.html
- QDataStream: https://doc.qt.io/qt-5/qdatastream.html
- QTcpSocket: https://doc.qt.io/qt-5/qtcpsocket.html

### 本项目文档
- VimUsing 服务器学习指南：`VimUsing/LEARNING_GUIDE.md`
- 客户端学习日志：`client/claude.md`

---

## 🎓 学习检查清单

在每个阶段结束时，确认以下问题能够回答：

### 协议层阶段
- [ ] 为什么要用 QDataStream 而不是直接 append？
- [ ] 什么是大端字节序？为什么网络协议使用大端？
- [ ] 如何处理网络数据的粘包和分包？
- [ ] `quint8` vs `quint32` 的区别和使用场景？

### 网络层阶段
- [ ] QTcpSocket 的信号槽机制是什么？
- [ ] 如何处理连接失败和断线重连？
- [ ] 什么是异步I/O？与同步I/O的区别？
- [ ] 如何在Qt中实现非阻塞网络编程？

### 业务层阶段
- [ ] 如何设计服务层的接口？
- [ ] 如何处理异步操作的回调？
- [ ] 错误处理的最佳实践？

---

## 🔄 版本历史

| 日期 | 版本 | 更新内容 |
|-----|------|---------|
| 2025-10-18 | v1.0 | 初始版本，记录项目总览和当前进度 |

---

## 💬 给未来 Claude 的话

当你接手这个会话时：

1. **首先阅读本文档**，了解项目状态和学习目标
2. **检查 `client/claude.md`**，查看详细的学习进度
3. **确认当前任务**：查看"进行中"部分
4. **遵循教学原则**：不要直接给答案，引导学习者思考
5. **记录学习过程**：及时更新 `claude.md`
6. **保持耐心**：学习是渐进的，不要急于推进进度

**学习者的目标不是快速完成项目，而是深入理解每一个知识点。**

---

## 📞 快速状态检查命令

当新会话开始时，可以运行以下命令快速了解状态：

```bash
# 查看项目结构
ls -la

# 查看已有的代码文件
find . -name "*.cpp" -o -name "*.h" | grep -v build | grep -v ".git"

# 查看测试代码
ls -la draft/

# 查看学习日志
cat docs/QtCSLearn/QtClient/LEARNING-LOG.md

# 检查构建状态
mkdir -p build && cd build && cmake .. && make
```

---
**注意，次文档的框架不应该被改变，只能改变其进度的改变，和各目标的更新。详细的学习情况应该在claude.md中记录。**
**祝学习顺利！记住：理解比速度更重要。**
