# FamilyKey Graph

FamilyKey 项目的 The Graph Subgraph - 用于索引和查询 DeadManSwitch（死人开关）模块的区块链数据。

## 📖 项目简介

本项目是一个基于 The Graph 协议的 Subgraph，用于索引以太坊上 DeadManSwitch 模块的智能合约事件。通过本 Subgraph，前端应用可以使用 GraphQL 快速查询合约状态、历史事件和相关数据，而无需直接与区块链交互。

## ✨ 功能特性

- 🔍 **实时索引**：自动索引所有 DeadManSwitch 相关事件
- 📊 **GraphQL API**：提供强大的 GraphQL 查询接口
- 🔄 **状态追踪**：追踪所有签到、索赔开始、取消和完成事件
- 📈 **历史记录**：完整的事件历史记录和时间戳
- ⚡ **高性能查询**：通过 The Graph 网络实现快速数据检索

## 🏗️ 数据模型

### 核心实体

#### DeadManSwitch（死人开关）
- `id`: 合约地址
- `safe`: Safe 钱包地址
- `beneficiary`: 受益人地址
- `heartbeatInterval`: 心跳间隔时间
- `challengePeriod`: 挑战期时长
- `lastCheckIn`: 最后签到时间
- `claimReadyAt`: 索赔就绪时间
- `currentOwner`: 当前所有者
- `isClaimActive`: 索赔是否激活
- `createdAt/updatedAt`: 创建和更新时间

#### 事件实体
- **CheckInEvent**: 签到事件
- **ClaimStartedEvent**: 索赔开始事件
- **ClaimCancelledEvent**: 索赔取消事件
- **ClaimFinalizedEvent**: 索赔完成事件

## 🚀 快速开始

### 前置要求

- Node.js >= 16
- npm 或 yarn
- Graph CLI

### 安装依赖

```bash
npm install
```

### 配置

1. 修改 `subgraph.yaml` 中的合约地址和起始块：

```yaml
dataSources:
  - kind: ethereum
    name: DeadManSwitchModule
    network: mainnet  # 或其他网络如 sepolia, goerli 等
    source:
      address: "YOUR_CONTRACT_ADDRESS"
      startBlock: YOUR_START_BLOCK
```

2. 确保 ABI 文件路径正确：

```yaml
abis:
  - name: DeadManSwitchModule
    file: ../contracts/out/DeadManSwitchModule.sol/DeadManSwitchModule.json
```

### 生成代码

```bash
npm run codegen
```

此命令会根据 `schema.graphql` 和 `subgraph.yaml` 生成 TypeScript 类型定义。

### 构建

```bash
npm run build
```

## 📦 部署

### 部署到 The Graph Studio

1. 在 [The Graph Studio](https://thegraph.com/studio/) 创建一个新的 Subgraph

2. 获取你的部署密钥并认证：

```bash
graph auth --studio <DEPLOY_KEY>
```

3. 部署 Subgraph：

```bash
npm run deploy
```

### 本地开发部署

1. 启动本地 Graph Node（需要 Docker）

2. 创建本地 Subgraph：

```bash
npm run create-local
```

3. 部署到本地节点：

```bash
npm run deploy-local
```

4. 删除本地 Subgraph：

```bash
npm run remove-local
```

## 🔍 GraphQL 查询示例

### 查询所有 DeadManSwitch

```graphql
{
  deadManSwitches {
    id
    safe
    beneficiary
    heartbeatInterval
    challengePeriod
    lastCheckIn
    claimReadyAt
    currentOwner
    isClaimActive
    createdAt
    updatedAt
  }
}
```

### 查询特定地址的 DeadManSwitch

```graphql
{
  deadManSwitch(id: "0x...") {
    id
    safe
    beneficiary
    lastCheckIn
    isClaimActive
    checkIns(first: 10, orderBy: timestamp, orderDirection: desc) {
      timestamp
      blockNumber
      txHash
    }
  }
}
```

### 查询最近的签到事件

```graphql
{
  checkInEvents(first: 20, orderBy: timestamp, orderDirection: desc) {
    id
    deadManSwitch {
      id
      safe
    }
    timestamp
    blockNumber
    txHash
  }
}
```

### 查询激活的索赔

```graphql
{
  deadManSwitches(where: { isClaimActive: true }) {
    id
    beneficiary
    claimReadyAt
    claimStartedEvents(first: 1, orderBy: timestamp, orderDirection: desc) {
      timestamp
      claimReadyAt
    }
  }
}
```

### 查询索赔历史

```graphql
{
  claimFinalizedEvents(first: 10, orderBy: timestamp, orderDirection: desc) {
    id
    deadManSwitch {
      id
      safe
    }
    oldOwner
    newOwner
    timestamp
    txHash
  }
}
```

## 🛠️ 开发指南

### 项目结构

```
.
├── schema.graphql          # GraphQL 数据模型定义
├── subgraph.yaml          # Subgraph 配置文件
├── src/
│   └── mapping.ts         # 事件处理逻辑
├── package.json           # 项目依赖和脚本
└── tsconfig.json          # TypeScript 配置
```

### 修改数据模型

1. 编辑 `schema.graphql` 文件
2. 运行 `npm run codegen` 重新生成类型
3. 更新 `src/mapping.ts` 中的处理逻辑
4. 重新构建和部署

### 添加新的事件处理器

1. 在 `subgraph.yaml` 的 `eventHandlers` 部分添加新事件
2. 在 `src/mapping.ts` 中实现相应的处理函数
3. 运行 `npm run codegen` 和 `npm run build`

## 🧪 测试

本项目使用 Matchstick 进行单元测试：

```bash
# 安装测试依赖
npm install --save-dev matchstick-as

# 运行测试
npm test
```

## 📚 相关资源

- [The Graph 官方文档](https://thegraph.com/docs/)
- [AssemblyScript 文档](https://www.assemblyscript.org/)
- [Graph CLI 文档](https://github.com/graphprotocol/graph-tooling/tree/main/packages/cli)
- [FamilyKey 项目主仓库](https://github.com/yourusername/familykey)

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

MIT License

## 🔗 相关链接

- **The Graph Studio**: https://thegraph.com/studio/
- **Graph Explorer**: https://thegraph.com/explorer/
- **项目仓库**: https://github.com/yourusername/familykey

## ⚠️ 注意事项

1. 部署前请确保合约地址和起始块号正确
2. ABI 文件路径需要指向正确的合约编译输出
3. 网络配置需要与实际部署的链匹配
4. 首次部署后可能需要等待几分钟进行同步

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 [GitHub Issue](https://github.com/yourusername/familykey/issues)
- 发送邮件至：your-email@example.com

---

**由 FamilyKey 团队维护** 💙