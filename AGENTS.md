# akong-CLI 项目文档

## 项目概述

akong-CLI 是一个用 Go 语言编写的命令行工具，采用 Cobra 框架构建。这是一个情感陪伴型的 CLI 应用，名为"阿空"，可以与用户进行对话互动，保存用户的输入和回复记录，并提供随机语录和回忆功能。

**核心特性：**
- 对话互动：用户可以向阿空输入消息，阿空会根据关键词匹配返回相应的温情回复
- 记忆保存：自动将对话记录保存到本地文件中（`~/.akong/memories.json`）
- 随机语录：提供阿空预设的每日碎碎念，每次随机抽取
- 回顾回忆：从历史对话记录中随机抽取一条旧对话重温

## 技术栈

- **语言**：Go 1.25.5
- **核心框架**：[Cobra](https://github.com/spf13/cobra) v1.10.2（CLI 框架）
- **颜色输出**：[fatih/color](https://github.com/fatih/color) v1.18.0
- **项目结构**：
  ```
  akong-CLI/
  ├── main.go           # 程序入口
  ├── cmd/              # CLI 命令定义
  │   ├── root.go       # 根命令
  │   ├── say.go        # say 命令
  │   ├── today.go      # today 命令
  │   └── random.go     # random 命令
  └── internal/akong/   # 内部逻辑
      ├── bot.go        # 机器人回复逻辑
      └── memory.go     # 记忆存储与检索
  ```

## 构建与运行

### 构建

```bash
go build -o akong
```

### 运行

```bash
# 查看帮助
./akong --help

# 对话互动
./akong say <你的消息>

# 获取随机语录
./akong today

# 回顾历史对话
./akong random
```

### 测试

```bash
go test ./...
```

## 开发约定

### 代码风格
- 使用 Go 标准格式化工具：`go fmt ./...`
- 函数和变量使用驼峰命名法
- 包注释遵循 Go 文档规范

### 命令规范
- 所有子命令定义在 `cmd/` 目录下
- 每个命令文件通过 `init()` 函数将命令添加到 `rootCmd`
- 使用 `color` 包为帮助文本和输出添加颜色，增强用户体验

### 数据存储
- 用户对话记录以 JSON 格式存储在 `~/.akong/memories.json`
- 使用 `encoding/json` 进行序列化和反序列化
- 每条记录包含时间戳、用户输入和机器人回复

### 回复逻辑
- 关键词匹配：`bot.go` 中的 `brain` map 定义了关键词与回复的映射
- 随机选择：每个关键词对应多条回复，使用 `math/rand` 随机选择
- 默认回复：未匹配关键词时返回随机语录

## 命令说明

### `say` 命令
与阿空对话。接收用户输入，匹配关键词返回回复，并将对话保存到本地。

```bash
./akong say 今天好累
```

### `today` 命令
获取阿空的随机语录。从预设的语录列表中随机抽取一条显示。

```bash
./akong today
```

### `random` 命令
回顾历史对话。从保存的对话记录中随机抽取一条显示，包括时间戳、用户输入和阿空回复。

```bash
./akong random
```

## 扩展指南

### 添加新回复
在 `internal/akong/bot.go` 的 `brain` map 中添加新的关键词和回复列表。

### 添加新命令
1. 在 `cmd/` 目录下创建新的命令文件（如 `newcmd.go`）
2. 定义 `cobra.Command` 并实现 `Run` 函数
3. 在 `init()` 中使用 `rootCmd.AddCommand()` 注册命令

### 修改存储位置
在 `internal/akong/memory.go` 的 `getFilePath()` 函数中修改存储目录路径。