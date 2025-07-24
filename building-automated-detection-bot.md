# Endpoint Security: My Journey Building a Detection Rate Bot

## Intro

Without revealing too much about the custom tool I built, I want to walk through the high-level steps I took so any researcher can build their own bot to detect and report stats on threat actor activities.

My detection rate bot captured newly deployed domains from threat actors and ran each of these domains through various third-party APIs. The output included detection rate stats for each third party and a full report of which domains were detected. I set up this bot to run weekly and send my team and me a report. *Key info about threat actors and third-party tools has been redacted for obvious reasons.

![alt text](image.png)

## Tech Stack

You'll need some technical experience and access to a few tools to recreate this kind of bot:

- Python programming experience (Cursor AI is your friend, lol)
- API access to an infrastructure search engine (e.g., Shodan, ZoomEye)
- API access to third-party domain flagging tools
- Webhook to your platform of choice (Slack, Discord, MS Teams)
- AWS cloud experience

## Building the Detection Bot

When building this tool, my primary focus was automation. I wanted it to be easy for anyone on my team to set up.

I built it as a CLI tool. That way, I could automate it later with a cron job, but also run it manually whenever needed.

The bot has four main components:
- URL collection using a search engine tool
- URL analysis using third-party detection tools
- Reporting
- Logging

Here's my pseudocode for how I wanted this bot to operate:

```
# Organize threat actor IOCs into searchable content
# API call to pull latest (within 7 days) domains based on the IOCs
# Feed each threat actor domain through third-party APIs
# Log each response from the above API calls
# Analyze logged responses
# Create a caching system to prevent duplicate calls
# Create a DB to evaluate data over time
# Create a report from the analysis
# Send analysis via webhook
```

Once I had this simple CLI tool implemented, I set up automated reporting.

I used an AWS EC2 instance to host the bot, including its SQLite DB.

Within the EC2 instance, I scheduled a cron job to execute the CLI tool every week.

Later, I used an AWS Lambda function plus EventBridge Scheduler to optimize the bot's cost. This step isn't absolutely necessary, but I figured I might as well learn how to build a bot on a budget.

## Conclusion

If you complete the above steps, you can get fancy and add even better features to your bot. I eventually modified the bot to show overall statistics more clearly. Overall, this project significantly impacted how engineers and decision-makers selected third-party vendors. After selection, this tool was used to track vendor progress over time.

<img src="image-1.png" width="500" height="500">

## Lessons Learned

- Leveraging Cursor AI to become a 10x security engineer
- DB optimization / building my own caching mechanism
- Importance of extensive logging for in-house tools
- Building custom alerts for teams using webhooks
- Using cron jobs for a production-ready alerting system
- AWS basics










