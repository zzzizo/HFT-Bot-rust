# 🚀 High-Frequency Trading Bot (Rust)

[![Rust](https://img.shields.io/badge/rust-1.70+-orange.svg)](https://www.rust-lang.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Safety: Testnet First](https://img.shields.io/badge/Safety-Testnet%20First-green.svg)](#safety-first)

A high-performance, real-time cryptocurrency trading bot built in Rust with advanced risk management, multiple trading strategies, and comprehensive API integration.

## ✨ Features

- **🏎️ High-Frequency Trading**: Sub-second execution with optimized async architecture
- **📊 Real-Time Market Data**: Live price feeds and orderbook integration via Binance API
- **🧠 Multiple Strategies**: Momentum and mean reversion algorithms with pluggable architecture
- **🛡️ Advanced Risk Management**: Position limits, stop-loss, daily loss limits, and order validation
- **🧪 Safe Testing**: Comprehensive testnet support with paper trading
- **⚡ Concurrent Processing**: Multi-threaded price collection and strategy execution
- **📈 Live Monitoring**: Real-time logging and performance metrics
- **🔧 Configurable**: Environment-based configuration with flexible parameters

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Market Data   │────│  Trading Bot    │────│ Order Executor  │
│     Feed        │    │   (Strategies)  │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Binance API    │    │ Risk Manager    │    │   Position      │
│   (REST/WS)     │    │                 │    │   Tracker       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 📋 Prerequisites

- **Rust 1.70+** ([Install Rust](https://rustup.rs/))
- **Binance Account** (for API keys)
- **Git** ([Install Git](https://git-scm.com/downloads))

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/hft-trading-bot.git
cd hft-trading-bot
```

### 2. Install Dependencies

```bash
cargo build
```

### 3. Set Up Testnet API Keys

1. **Get Binance Testnet Keys** (Safe - No Real Money):
   - Visit [Binance Spot Testnet](https://testnet.binance.vision/)
   - Login with GitHub account
   - Generate HMAC_SHA256 API keys

2. **Configure Environment**:
```bash
# Create environment file
cp setup_env.example.sh setup_env.sh

# Edit with your testnet keys
nano setup_env.sh
```

3. **Update setup_env.sh**:
```bash
#!/bin/bash
export BINANCE_API_KEY="your_testnet_api_key_here"
export BINANCE_SECRET_KEY="your_testnet_secret_key_here"
export USE_TESTNET="true"
export RUST_LOG="info"
```

### 4. Run the Bot

```bash
# Load environment and run
source setup_env.sh
cargo run
```

## 📖 Detailed Setup

### Development Environment

```bash
# Debug build (faster compilation)
cargo build

# Release build (optimized performance)
cargo build --release

# Run with detailed logging
RUST_LOG=debug cargo run

# Run tests
cargo test
```

### Configuration Options

Environment variables for customization:

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `BINANCE_API_KEY` | Binance API key | **Required** | `abc123...` |
| `BINANCE_SECRET_KEY` | Binance secret key | **Required** | `def456...` |
| `USE_TESTNET` | Use testnet (true/false) | `true` | `false` |
| `RUST_LOG` | Logging level | `info` | `debug` |

### Trading Symbols

Modify symbols in `src/main.rs`:

```rust
let symbols = vec![
    "BTCUSDT".to_string(),
    "ETHUSDT".to_string(),
    "ADAUSDT".to_string(),
    "SOLUSDT".to_string(),
];
```

## 🎯 Trading Strategies

### Momentum Strategy

Identifies and trades based on price momentum:

```rust
Box::new(MomentumStrategy::new(
    10,     // lookback_period (data points)
    0.002   // momentum_threshold (0.2%)
))
```

### Mean Reversion Strategy

Trades when prices deviate from their mean:

```rust
Box::new(MeanReversionStrategy::new(
    20,     // lookback_period (data points)  
    0.03    // deviation_threshold (3%)
))
```

### Custom Strategy

Implement the `TradingStrategy` trait:

```rust
pub struct YourStrategy {
    // Your parameters
}

impl TradingStrategy for YourStrategy {
    fn analyze(&self, prices: &[Price], orderbook: &OrderBook) -> Option<TradingSignal> {
        // Your logic here
    }
    
    fn name(&self) -> &str {
        "YourStrategy"
    }
}
```

## 🛡️ Risk Management

### Default Risk Parameters

```rust
RiskParams {
    max_position_size: 1000.0,      // Maximum position per symbol
    max_loss_per_trade: 100.0,      // Maximum loss per trade
    max_daily_loss: 500.0,          // Daily loss limit
    stop_loss_pct: 0.02,            // 2% stop loss
    take_profit_pct: 0.04,          // 4% take profit
}
```

### Position Monitoring

```bash
# View real-time positions
tail -f logs/positions.log

# Monitor risk metrics  
tail -f logs/risk.log
```

## 📊 Expected Output

### Successful Startup
```
🚀 Starting bot in 🧪 TESTNET mode
✅ API connection successful. BTC price: $43,251.23
🎯 Starting real trading with symbols: ["BTCUSDT", "ETHUSDT"]
📊 Real price for BTCUSDT: $43,251.23
📈 Checking BTCUSDT with 5 price points
```

### Trading Activity
```
📊 BTCUSDT price change: 0.025% (threshold: 0.001%)
🎯 Signal from MomentumStrategy: TradingSignal { 
    symbol: "BTCUSDT", 
    action: Buy, 
    confidence: 0.8, 
    target_price: 43251.23, 
    quantity: 0.001 
}
🧪 TESTNET: Would submit order: Order { id: "abc-123", ... }
✅ Order submitted successfully: testnet_abc-123
```

## 🔧 Advanced Configuration

### Performance Tuning

```rust
// High-frequency settings (src/main.rs)
tokio::time::sleep(Duration::from_millis(50)).await;  // 20 Hz

// Conservative settings  
tokio::time::sleep(Duration::from_secs(5)).await;     // 0.2 Hz
```

### Strategy Sensitivity

```rust
// Ultra-sensitive (many trades)
MomentumStrategy::new(5, 0.0001)   // 0.01% threshold

// Conservative (fewer trades)
MomentumStrategy::new(20, 0.01)    // 1% threshold
```

## 🚨 Safety First

### ⚠️ IMPORTANT DISCLAIMERS

1. **Start with Testnet**: Always test extensively on testnet before live trading
2. **Paper Trading**: Verify strategies with simulated trading first
3. **Risk Management**: Never risk more than you can afford to lose
4. **Market Risks**: Cryptocurrency trading involves substantial risk
5. **No Guarantees**: Past performance does not guarantee future results

### Safe Development Workflow

```bash
# 1. Testnet Development
export USE_TESTNET="true"
cargo run

# 2. Paper Trading (coming soon)
export PAPER_TRADING="true"
cargo run

# 3. Live Trading (when ready)
export USE_TESTNET="false"
cargo run
```

## 🧪 Testing

### Unit Tests
```bash
# Run all tests
cargo test

# Run specific test module
cargo test test_momentum_strategy

# Run with output
cargo test -- --nocapture
```

### Integration Tests
```bash
# Test API connections
cargo test test_binance_api

# Test risk management
cargo test test_risk_validation
```

## 📚 API Documentation

### Market Data
- `get_price(symbol)` - Get current price
- `get_orderbook(symbol)` - Get bid/ask data
- `get_24hr_volume(symbol)` - Get trading volume

### Order Management  
- `submit_order(order)` - Submit trading order
- `cancel_order(symbol, order_id)` - Cancel order
- `get_balance(asset)` - Get account balance

### Risk Management
- `validate_order(order, price)` - Validate before submission
- `update_position(symbol, quantity, price)` - Track positions

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md).

### Development Setup

```bash
# Fork and clone the repository
git clone https://github.com/yourusername/hft-trading-bot.git

# Create feature branch
git checkout -b feature/your-feature-name

# Make changes and test
cargo test
cargo build --release

# Submit pull request
git push origin feature/your-feature-name
```

### Code Style

```bash
# Format code
cargo fmt

# Run linter
cargo clippy

# Check for issues
cargo audit
```

## 📈 Performance

### Benchmarks

| Metric | Value |
|--------|-------|
| Order Execution | < 50ms |
| Price Update Frequency | 20 Hz |
| Memory Usage | < 100MB |
| CPU Usage | < 10% |

### Optimization Tips

- Use release builds for production: `cargo build --release`
- Adjust update frequencies based on strategy needs
- Monitor system resources during operation
- Consider WebSocket connections for faster data

## 🔗 Supported Exchanges

### Current
- ✅ **Binance Spot** (REST API)
- ✅ **Binance Testnet** (Safe testing)

### Planned
- 🔄 **Binance WebSocket** (Real-time data)
- 🔄 **Binance Futures** (Derivatives trading)
- 🔄 **Other Exchanges** (Coinbase, Kraken, etc.)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⚖️ Legal Disclaimer

This software is for educational and research purposes only. The authors are not responsible for any financial losses incurred through the use of this software. Trading cryptocurrencies involves substantial risk of loss and is not suitable for all investors. Please consult with a qualified financial advisor before making any trading decisions.

## 🆘 Support

### Documentation
- [API Documentation](docs/api.md)
- [Strategy Development Guide](docs/strategies.md)
- [Risk Management Best Practices](docs/risk-management.md)

### Community
- [GitHub Issues](https://github.com/yourusername/hft-trading-bot/issues)
- [Discussions](https://github.com/yourusername/hft-trading-bot/discussions)
- [Discord Server](https://discord.gg/your-server)

### Getting Help

```bash
# View help
cargo run -- --help

# Check logs
tail -f logs/trading.log

# Debug mode
RUST_LOG=debug cargo run
```

---

**⭐ If this project helped you, please give it a star!**

**🚀 Happy Trading! (Safely on testnet first)**
