# Incident and Event Response

## Table of Contents
- [GuardDuty](#GuardDuty)
- [Amazon Inspector](#Amazon-Inspector)
- [Amazon Kinesis](#Amazon-Kinesis)

# GuardDuty
A threat detection service that continuously monitors for malicious or unauthorized behavior

It does this by monitoring for:
- Unusual API calls
- Unauthorized deployments
- Compromised instances

It uses:
- Threat intelligence feeds
- Machine Learning
- CloudWatch Events

How it works:
1. Enable service
2. Analyze continuously
3. Detect Threats using machine learning
4. Act
  - Detailed analysis is available in console
  - Can integrate logging, alerting, or trigger lambda

# Amazon Inspector
Automated service that assess your applications for vulnerabilities and produces a security findings report. Mainly based around protecting EC2 instances.

- Identify security issuesu
- API driven
- Reduces risk by warning you of risk before it's a problem
- Leverage expertise: experts researching potential security issues
- Define and Enforce standards

How it works:
- Network assessments (no agent needed)
  - network configuration check (see if ports are reachable from outside VPC)
- Host assessment (Inspector Agent on EC2)
  - Can be automatically installed via Systems Manager run command
- Can run weekly or once

# Amazon Kinesis
Collect, process, and analyze video and data streams in real time

Made of 4 services
- Kinesis Data Analytics
- Kinesis Data Firehose
- Kinesis Data Streams
- Kinesis Video Streams

### Kinesis Data Analytics
- Analyze streaming data
- Respond in real time
- Query data using SQL
- completely managed service
- pay as you go (for what you use)

### Kinesis Firehose
- Deliver streaming data
- No applications to manage
- Just configure data producer to send data to Firehose
  - Firehose send data to:
    - S3
    - Redshift
    - Amazon ElasticSearch
    - Splunk
  - Accepts records in chunks up to 1000 kb
- Data can be transformed

### Kinesis Data Streams
- Collect streaming data
- Massively scalable
- Capture GBs per second
- data is available in milliseconds
- durable, streamed across 3 data centers
- data stored for 7 days
- Elastic, dynamically scale

### Kinesis Video Streams
- Collect streaming video
- Handle ingestion from millions of devices
- Enables live and on-demand playback
- Can take advantage of Amazon Rekognition and Machine Learning Frameworks
- Access data through APIs
- Build real-time, video-enabled applications


### Kinesis Overview

- Kinesis Data Analytics: Analyze streaming data using SQL
- Kinesis Firehose: Deliver streaming data to another AWS service, e.g. S3
- Kinesis Data Streams: Collect streaming data and do things with it (e.g. create dashboard)
- Kinesis Video Streams: Collect streaming video data and do things with it

Exam hint: Kinesis will be an answer if it has to do with large amounts of data
