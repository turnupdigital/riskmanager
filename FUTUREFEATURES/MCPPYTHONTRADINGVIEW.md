
Search MCP servers, tools, and more
MindLayer TradingView MCP Agent
by MindLayer25
Finance
Cryptocurrency
API Testing
Python
MIT License
Need Help?
View Source Code
Report Issue

Which integrations are available for this server?
MindLayer TradingView MCP Agent
A powerful integration system that connects TradingView's Pine Script indicators with MindLayer's MCP (Model Context Protocol) for advanced cryptocurrency trading signals based on RSI and Stochastic RSI.

Overview
This system consists of three main components:

Pine Script Indicator: A TradingView indicator that analyzes RSI and Stochastic RSI to generate buy/sell signals.
MCP Agent: A Python application that processes these signals and communicates with MCP-enabled systems.
REST API: An HTTP API that allows programmatic access to all MCP agent functionality.
Features
📊 RSI & Stochastic RSI Analysis: Generates signals based on these powerful momentum indicators
🔄 Multi-Timeframe Analysis: Confirms signals using higher timeframe data
📱 Real-time Alerts: Sends alerts via TradingView's webhook system
🤖 MCP Integration: Seamlessly integrates with MindLayer's Model Context Protocol
📈 Adaptive Confidence Levels: Each signal includes a strength indicator (1-5)
🛡️ Risk Management: Configurable risk profiles based on your trading style
🌐 RESTful API: Access all functionality programmatically via HTTP API
Setup Instructions
TradingView Indicator Setup
Log in to your TradingView account
Go to Pine Editor
Create a new indicator and paste the contents of MindLayer_MCP_Signal.pine
Save and add to chart
Configure the indicator settings according to your preferences
System Setup
Clone this repository
Install required dependencies:

pip install -r requirements.txt
Configure your settings (edit config.py or use environment variables)
Start the system using the launcher script:

# Run just the MCP agent
python run.py agent

# Run just the API server (which includes the agent)
python run.py api

# Run both the agent and API server separately (advanced)
python run.py both
Command Line Options
The run.py script accepts several command-line arguments:


# Set custom API port
python run.py api --port 8080

# Set custom webhook port
python run.py agent --webhook-port 9000

# Run in debug mode
python run.py api --debug

# Display help
python run.py --help
TradingView Alert Setup
Open your chart with the MindLayer MCP Signal indicator
Right-click on the indicator and select "Add Alert"
Set condition to trigger on "MindLayer MCP Buy Signal" or "MindLayer MCP Sell Signal"
In the webhook URL field, enter your MCP agent's webhook URL (e.g., http://your-server:8000) or the API webhook endpoint (e.g., http://your-server:5000/api/webhook)
In the message field, paste the following JSON template:

{
    "ticker": "{{ticker}}",
    "type": "{{strategy.order.action}}",
    "confidence": {{plot("Buy Signal")}} or {{plot("Sell Signal")}},
    "price": {{close}},
    "rsi": {{rsi}},
    "stoch": {{stoch}},
    "htf_rsi": {{plot("HTF RSI")}},
    "htf_stoch": {{plot("HTF Stoch")}}
}
Save the alert
Configuration
Environment Variables
You can configure the system using environment variables (create a .env file):


# API Configuration
API_KEY=your_api_key_here
API_SECRET=your_api_secret_here

# Webhook Configuration
WEBHOOK_SECRET=your_webhook_secret_here
WEBHOOK_PORT=8000

# API Configuration
API_PORT=5000
DEBUG=false

# MCP Connection Settings
MCP_API_URL=https://api.mindlayer.io/v1
MCP_WEBSOCKET_URL=wss://api.mindlayer.io/ws

# Trading Configuration
TRADING_ENABLED=false
RISK_TOLERANCE=moderate
MIN_CONFIDENCE=3

# RSI/Stochastic RSI Configuration
RSI_OVERSOLD=30
RSI_OVERBOUGHT=70
STOCH_OVERSOLD=20
STOCH_OVERBOUGHT=80
Pine Script Customization
The TradingView indicator is highly customizable:

Risk Profile: Conservative, Moderate, or Aggressive
RSI Parameters: Change length and overbought/oversold thresholds
Stochastic RSI Parameters: Adjust K, D periods and thresholds
Visual Settings: Customize colors and display options
Signal Interpretation
Buy Signals
Strong Buy: Green arrow with high confidence rating (4-5)
Moderate Buy: Light green arrow with medium confidence rating (2-3)
Weak Buy: Dotted green arrow with low confidence rating (1)
Sell Signals
Strong Sell: Red arrow with high confidence rating (4-5)
Moderate Sell: Light red arrow with medium confidence rating (2-3)
Weak Sell: Dotted red arrow with low confidence rating (1)
How It Works
The Pine Script indicator analyzes price action using RSI and Stochastic RSI
When conditions meet your configured criteria, it displays a buy/sell signal on the chart
TradingView sends an alert via webhook to your MCP agent or API
The MCP agent processes the signal and communicates with MCP-enabled systems
(Optional) The agent can execute trades based on these signals
REST API Documentation
The system includes a comprehensive REST API for programmatic access to all functionality.

API Endpoints
Signal Management
GET /api/signals - Get all trading signals
GET /api/signals?symbol=BTCUSDT - Get signals for a specific symbol
POST /api/signals - Manually create a new signal
Indicator Values
GET /api/indicators - Get all indicator values
GET /api/indicators?symbol=BTCUSDT - Get indicator values for a specific symbol
Agent Control
GET /api/status - Get current agent status
POST /api/start - Start the MCP agent
POST /api/stop - Stop the MCP agent
Configuration
GET /api/config - Get current configuration
PUT /api/config - Update configuration settings
Webhook
POST /api/webhook - Receive webhook from TradingView
API Documentation
GET /api/docs - Get detailed API documentation
API Usage Examples
Get Current Agent Status

curl http://localhost:5000/api/status
Get All Signals

curl http://localhost:5000/api/signals
Create a Manual Signal

curl -X POST http://localhost:5000/api/signals \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "BTCUSDT",
    "type": "BUY",
    "price": 50000.0,
    "confidence": 4,
    "rsi": 25.5,
    "stoch": 15.2
  }'
Update Configuration

curl -X PUT http://localhost:5000/api/config \
  -H "Content-Type: application/json" \
  -d '{
    "trading_enabled": true,
    "min_confidence": 4,
    "rsi_oversold": 25
  }'
Requirements
Python 3.7+
TradingView account (Pro plan recommended for webhook alerts)
Server or cloud instance to run the MCP agent and API (if using webhooks)
System Architecture

┌─────────────────┐     ┌──────────────────┐     ┌────────────────┐
│   TradingView   │     │   MCP Agent or   │     │   MCP/Trading  │
│   Pine Script   │────▶│    API Server    │────▶│     System     │
└─────────────────┘     └──────────────────┘     └────────────────┘
                               ▲     ▲
                               │     │
                               │     │
                        ┌──────┘     └────────┐
                        │                     │
                  ┌───────────┐       ┌─────────────┐
                  │ External  │       │   Manual    │
                  │   API     │       │  Commands   │
                  │  Clients  │       │ (CLI/Config)│
                  └───────────┘       └─────────────┘
Best Practices
Always test thoroughly in a paper trading environment before using real funds
Combine these signals with other analysis and risk management techniques
Higher timeframe signals tend to be more reliable than very short timeframes
Consider market conditions that might impact signal reliability
Secure your API server behind proper authentication if exposing to the internet
Support
If you encounter issues or have questions, please open an issue on this repository.

License
This project is licensed under the MIT License - see the LICENSE file for details.

Disclaimer
Trading cryptocurrency involves substantial risk. Past performance of this indicator does not guarantee future results. Always use proper risk management and never trade with funds you cannot afford to lose.