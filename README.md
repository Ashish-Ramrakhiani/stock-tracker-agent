# AI Stock Market Tracker

I built this automated stock tracking system because I got tired of constantly checking stock prices and missing important market movements. This little agent watches my portfolio, researches what's happening when prices move significantly, and texts me concise summaries so I stay informed without being glued to my phone.

The best part? I can control everything via SMS - add stocks, remove them, or check prices just by texting the bot.

## What It Does

This system monitors stocks for significant price changes (>1% movement) and automatically:
- Researches relevant news and market events using AI
- Sends me SMS alerts with concise explanations of what caused the movement  
- Lets me manage my watchlist through simple text commands
- Runs 24/7 as a web service that integrates with Twilio

## How I Use It

**Text Commands I Send:**
- "Start tracking NVDA" - adds NVIDIA to my watchlist
- "Stop watching MSFT" - removes Microsoft 
- "What's AAPL doing?" - gets current Apple stock price
- "Show my list" - displays all tracked stocks

**Automatic Alerts I Receive:**
- "TSLA UP 3.2%: Musk announces new Gigafactory expansion in Texas, production targets raised"
- "AAPL DOWN 2.1%: iPhone sales disappointing in China market, supply chain concerns"

## Setup & Installation

**What You'll Need:**
- Python 3.11 or newer
- OpenAI API key (for the research agent)
- Twilio account with a phone number (for SMS)

**Quick Start:**
```bash
git clone https://github.com/yourusername/stock-tracker-agent.git
cd stock-tracker-agent
pip install -r requirements.txt
cp .env.example .env
```

**Environment Variables:**
Edit your `.env` file with your credentials:
```env
OPENAI_API_KEY=your-openai-api-key
TWILIO_PHONE_NUMBER=your_twilio_phone_number  
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TARGET_PHONE_NUMBER=your_personal_phone_number
WEBHOOK_URL=your_public_webhook_url
```

## Running the System

**Production Mode:**
```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

**Testing Mode:**
```bash
python main.py -test
```
This runs a chat interface for testing and checks stocks every minute instead of hourly.

**Research a Specific Stock:**
```bash
python main.py -test -research AAPL
```

## Docker Deployment

I prefer running this in Docker for cleaner deployments:

```bash
docker build -t stock-tracker .
docker run -p 8000:8000 --env-file .env stock-tracker
```

Or use the included docker-compose file:
```bash
docker-compose up --build
```

## Twilio Configuration

1. Get a Twilio phone number and API credentials
2. Set your webhook URL to point to `your-domain.com/receive-message`
3. The system only responds to messages from your registered phone number for security

Make sure your `WEBHOOK_URL` in the `.env` file exactly matches what you configure in Twilio's console.

## Project Structure

```
stock-tracker-agent/
├── main.py              # FastAPI web server and entry point
├── lib/
│   ├── agent.py         # AI agents for research and message handling  
│   ├── stock_checker.py # Yahoo Finance integration
│   ├── tracker.py       # Stock monitoring logic
│   ├── sms.py          # Twilio SMS integration
│   └── tools.py        # Helper functions for agents
├── resources/
│   ├── alert_history.json    # Tracks daily alerts sent
│   └── tracker_list.json     # Your current watchlist
└── requirements.txt     # Python dependencies
```

## How the Magic Works

1. **Background Monitor**: Checks your tracked stocks every hour for price movements >1%
2. **Smart Research**: When movement detected, AI agent searches news and analyzes what happened  
3. **Concise Summaries**: Research gets condensed to SMS-friendly messages under 160 characters
4. **SMS Integration**: Sends alerts to your phone and accepts commands to manage the watchlist
5. **Rate Limiting**: Only sends one alert per stock per day to avoid spam

## Known Issues & Notes

- Stock data comes from Yahoo Finance via the yfinance library
- The 1% movement threshold is hardcoded but easy to adjust in `tracker.py`
- AI research uses GPT-4 for better analysis quality
- SMS messages are capped at 160 characters for cost efficiency
- Alert history prevents duplicate notifications on the same day

## Personal Tweaks I've Made

Feel free to customize these aspects:
- Adjust the price movement threshold in `lib/tracker.py`
- Modify the research prompt in `lib/agent.py` for different analysis styles
- Change the tracking frequency in `main.py` (currently hourly in production)
- Add more sophisticated filtering logic for alerts

---

Built this for my own portfolio management but figured others might find it useful. The code isn't perfect but it gets the job done and saves me from constantly checking stock apps.