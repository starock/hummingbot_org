# Hummingbot

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Python](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Discord](https://img.shields.io/discord/530578568154054663.svg?color=768AD4&label=discord&logo=https%3A%2F%2Fdiscordapp.com%2Fassets%2F8c9701b98ad4372b58f13fd9f65f966e.svg)](https://discord.hummingbot.io/)
[![Twitter](https://img.shields.io/twitter/follow/hummingbot_io.svg?style=social&label=hummingbot)](https://twitter.com/hummingbot_io)

> **开源算法交易框架，让高频交易变得民主化**

Hummingbot 是一个用 Python 和 Cython 构建的高性能、模块化的算法交易框架。它支持140多个交易所的做市、套利和其他交易策略，已帮助用户创造超过340亿美元的交易量。

## ✨ 核心特性

### 🚀 高性能架构
- **低延迟交易**：基于 Cython 优化的核心组件
- **事件驱动**：异步事件处理机制，毫秒级响应
- **内存优化**：高效的数据结构和缓存策略

### 🔗 广泛的交易所支持
- **140+ 交易所**：支持主流 CEX 和 DEX
- **统一接口**：一致的 API 抽象，轻松切换交易所
- **实时数据**：WebSocket 连接，实时订单簿和交易数据

### 📊 丰富的策略类型
- **做市策略**：纯做市、Avellaneda-Stoikov 模型
- **套利策略**：跨交易所套利、三角套利、AMM 套利
- **趋势策略**：TWAP、动量交易、网格交易
- **DeFi策略**：流动性挖矿、收益农场

### 🛠️ 开发者友好
- **模块化设计**：清晰的架构分层，易于扩展
- **插件系统**：自定义策略和连接器开发
- **类型安全**：基于 Pydantic 的配置验证
- **详细文档**：完整的 API 文档和教程

## 🚀 快速开始

### 前置要求

- Python 3.8 或更高版本
- Git
- Docker (可选，用于容器化部署)

### 安装方式

#### 方式一：源码安装 (推荐)

```bash
# 克隆仓库
git clone https://github.com/hummingbot/hummingbot.git
cd hummingbot

# 安装依赖
./install

# 编译 Cython 模块
./compile

# 启动 Hummingbot
./start
```

#### 方式二：Docker 安装

```bash
# 拉取镜像
docker pull hummingbot/hummingbot:latest

# 运行容器
docker run -it --name hummingbot-instance hummingbot/hummingbot:latest
```

#### 方式三：pip 安装

```bash
pip install hummingbot
```

### 首次运行

启动后，按照命令行向导完成初始配置：

```bash
# 创建密码
>>> password

# 导入或创建API密钥
>>> connect binance

# 创建策略
>>> create
```

## 💡 使用示例

### 创建做市策略

```python
# 通过 CLI 创建
>>> create
>>> pure_market_making

# 配置参数
Exchange: binance
Trading pair: BTC-USDT
Bid spread: 0.1%
Ask spread: 0.1%
Order amount: 0.01 BTC
```

### 使用 Strategy V2 (Python API)

```python
from hummingbot.strategy_v2.controllers.market_making_controller_base import MarketMakingControllerBase
from hummingbot.strategy_v2.models.executor_actions import CreateExecutorAction

class CustomMarketMakingController(MarketMakingControllerBase):
    async def update_processed_data(self):
        # 更新市场数据
        self.processed_data["mid_price"] = self.get_mid_price()
        
    def determine_executor_actions(self):
        # 决策逻辑
        if self.should_create_orders():
            return [
                CreateExecutorAction(
                    controller_id=self.config.id,
                    executor_config=self.get_executor_config()
                )
            ]
        return []
```

### 自定义脚本

```python
from hummingbot.strategy.script_strategy_base import ScriptStrategyBase

class MyTradingScript(ScriptStrategyBase):
    def on_tick(self):
        # 获取价格
        price = self.connectors["binance"].get_price("BTC-USDT", True)
        
        # 交易逻辑
        if self.should_buy(price):
            self.buy("binance", "BTC-USDT", 0.01)
```

## 🏗️ 架构概览

```
┌─────────────────────────────────────────────┐
│              用户界面层                        │
│  CLI Interface │ Dashboard │ API Gateway   │
├─────────────────────────────────────────────┤
│              应用层                          │
│    HummingbotApplication │ Strategy Mgmt   │
├─────────────────────────────────────────────┤
│              策略层                          │
│   Strategy V1 │ Strategy V2 │ Scripts      │
├─────────────────────────────────────────────┤
│              执行层                          │
│  Controllers │ Executors │ Order Mgmt     │
├─────────────────────────────────────────────┤
│              连接器层                        │
│    CEX Connectors │ DEX Connectors        │
├─────────────────────────────────────────────┤
│              核心层                          │
│   Clock System │ Event System │ Data Mgmt │
└─────────────────────────────────────────────┘
```

### 核心组件

- **策略引擎**：支持多种交易策略的执行框架
- **连接器系统**：统一的交易所接口抽象
- **事件系统**：高性能的事件驱动架构
- **时钟系统**：精确的时间控制和回测支持
- **风控系统**：内置的风险管理机制

## 📚 支持的交易所

### 中心化交易所 (CEX)
- Binance, Binance US
- Coinbase Pro, Kraken, KuCoin
- OKX, Gate.io, Huobi Global
- Bitfinex, Bittrex, Crypto.com
- 以及更多...

### 去中心化交易所 (DEX)
- Uniswap V2/V3, PancakeSwap
- SushiSwap, TraderJoe
- dYdX, Perpetual Protocol
- Balancer, Curve
- 以及更多...

[查看完整的交易所列表](https://docs.hummingbot.org/exchanges/)

## 🔧 配置指南

### 基础配置

```yaml
# config/client_config.yml
instance_id: "my-hummingbot"
log_level: "INFO"
kill_switch_enabled: true
kill_switch_rate: -0.20

# 策略配置示例
strategy: pure_market_making
exchange: binance
market: BTC-USDT
bid_spread: 0.1
ask_spread: 0.1
order_amount: 0.01
```

### 环境变量

```bash
# API 密钥 (推荐使用环境变量)
export BINANCE_API_KEY="your_api_key"
export BINANCE_SECRET_KEY="your_secret_key"

# 数据库配置
export DATABASE_URL="sqlite:///data/hummingbot.db"

# 日志级别
export LOG_LEVEL="INFO"
```

## 📊 监控和分析

### 内置监控
- 实时盈亏跟踪
- 订单执行统计
- 风险指标监控
- 性能分析报告

### 第三方集成
- **Grafana**：可视化仪表盘
- **Prometheus**：指标收集
- **Telegram**：实时通知
- **Discord**：社区集成

## 🤝 贡献指南

我们欢迎各种形式的贡献！

### 开发环境设置

```bash
# Fork 并克隆仓库
git clone https://github.com/yourusername/hummingbot.git
cd hummingbot

# 创建开发环境
conda create -n hummingbot python=3.9
conda activate hummingbot

# 安装开发依赖
pip install -r requirements-dev.txt

# 安装 pre-commit hooks
pre-commit install
```

### 贡献类型

- 🐛 **Bug 修复**
- ✨ **新功能开发**
- 📚 **文档改进**
- 🧪 **测试用例**
- 🔧 **新交易所连接器**
- 📈 **新交易策略**

### 提交流程

1. Fork 项目
2. 创建功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

查看 [贡献指南](CONTRIBUTING.md) 了解详细信息。

## 📖 文档和资源

### 官方文档
- 📘 [用户指南](https://docs.hummingbot.org/)
- 🔧 [开发者文档](https://docs.hummingbot.org/developers/)
- 📊 [策略库](https://docs.hummingbot.org/strategies/)
- 🎯 [教程集合](https://docs.hummingbot.org/academy/)

### 学习资源
- 🎥 [YouTube 频道](https://www.youtube.com/c/HummingbotChannel)
- 📝 [技术博客](https://blog.hummingbot.org/)
- 🎓 [在线课程](https://hummingbot.org/academy/)
- 📚 [案例研究](https://docs.hummingbot.org/case-studies/)

### 社区支持
- 💬 [Discord 社区](https://discord.hummingbot.io/)
- 🐦 [Twitter](https://twitter.com/hummingbot_io)
- 💼 [LinkedIn](https://www.linkedin.com/company/hummingbot/)
- 📧 [邮件列表](https://hummingbot.substack.com/)

## 🔒 安全考虑

### 最佳实践
- 🔐 **API 密钥安全**：使用只读权限，定期轮换密钥
- 💰 **资金管理**：从小额开始，设置止损限制
- 🔍 **代码审计**：运行前仔细审查策略代码
- 📊 **监控**：实时监控交易活动和系统状态

### 风险提示
⚠️ **重要提醒**：算法交易存在金融风险。请：
- 在实盘前充分测试策略
- 从小额资金开始
- 设置适当的风险限制
- 持续监控交易活动

## 📄 许可证

本项目采用 Apache License 2.0 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

感谢所有为 Hummingbot 项目做出贡献的开发者、交易者和社区成员。

### 核心贡献者
- CoinAlpha Team - 原始开发团队
- Hummingbot Foundation - 当前维护者
- 全球开源社区 - 持续贡献

### 支持者
- 交易所合作伙伴
- 机构用户
- 开源贡献者
- 社区用户

---

## 📞 联系我们

- 🌐 **官网**: [hummingbot.org](https://hummingbot.org/)
- 📧 **邮箱**: [support@hummingbot.org](mailto:support@hummingbot.org)
- 💬 **Discord**: [discord.hummingbot.io](https://discord.hummingbot.io/)
- 🐛 **Bug 报告**: [GitHub Issues](https://github.com/hummingbot/hummingbot/issues)

**⭐ 如果这个项目对您有帮助，请给我们一个 Star！**
