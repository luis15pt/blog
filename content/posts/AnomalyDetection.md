---
title: "Standard Deviations and Emergency Protocols: A Guide to Smarter Alerts"
date: 2025-01-19
author: "Luis Sarabando"
tags: ["monitoring", "prometheus", "grafana", "alerts", "anomaly-detection"]
---

Hello there! Maurice Moss speaking. Today I'm quite excited to share something rather brilliant that I've been working on in between explaining to Jen what a standard deviation is. It's about making your monitoring smarter than Roy's morning coffee routine!

## What's All This About Sigma Then?

You know what's really annoying? Getting woken up at 3 AM because your CPU usage hit 82% instead of the usual 80%. It's like having to restart your computer every time the mouse moves slightly to the left. But fear not - I've discovered something absolutely revolutionary: the 3-sigma rule!

Think of it like this: if your metrics were a game of Countdown, the 3-sigma rule would be the mathematical genius in the corner telling you what's normal and what's definitely not normal. 

## The Magic Formula (It's Not 0118 999 881 999 119 725... 3)

Here's the brilliant part - we can detect anomalies using this rather clever formula:
```promql
(
  avg_over_time(your_metric[$__rate_interval])
  - 
  avg_over_time(your_metric[1d])
)
/
stddev_over_time(your_metric[1d])
```

## What Do The Numbers Mean, Moss?
Let me explain this like I explained to Jen why computers need fans:

- Between -1 and +1: Everything's normal (68% of all values fall here, like Roy's morning mood)
- Between -2 and +2: Slightly unusual (95% of all values fall here, like Jen understanding a tech reference)
- Beyond -3 or +3: Something's definitely wrong (99.7% of values should be within this range, so anything beyond is like Richmond leaving the server room)

For example:
- A value of 1.5 means you're seeing a metric that's higher than 86% of normal values
- A value of -2.1 means you're seeing a metric that's lower than 98% of normal values
- A value of 3.2 means you're seeing something that happens less than 0.1% of the time


## Real World Examples (That Actually Work!)
Response Times

```promql
(
  avg_over_time(http_request_duration_seconds[$__rate_interval])
  - 
  avg_over_time(http_request_duration_seconds[1d])
)
/
stddev_over_time(http_request_duration_seconds[1d])
```

Queue Lengths
```promql
(
  avg_over_time(rabbitmq_queue_messages[$__rate_interval])
  -
  avg_over_time(rabbitmq_queue_messages[1d])
)
/
stddev_over_time(rabbitmq_queue_messages[1d])
```

## Setting Alert Levels (The Smart Way)

- Minor Alert (±2σ): "Have you tried turning it off and on again?"
- Major Alert (±3σ): "Are you sure it's plugged in?"
- Critical Alert (±4σ): "Quick, call the elders of the internet!"

# Conclusion

Remember, a good monitoring system is like a good relationship with your computer - it should understand what's normal and what's not, and only bother you when something is genuinely wrong.

If you need me, I'll be in my office, calculating standard deviations of my keyboard cleaning schedule.

P.S. Did you know? The probability of getting a false alert with 3-sigma is about the same as Jen successfully explaining what IT stands for!