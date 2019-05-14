# Monitoring and Logging

## Table of Contents
- [CloudWatch](#CloudWatch)
  - [CloudWatch Metrics](#CloudWatch-Metrics)
  - [CloudWatch Events](#CloudWatch-Events)
  - [CloudWatch Logs](#CloudWatch-Logs)
- [X-ray](#X-ray)

# CloudWatch
- Metric gathering
- Monitoring / Alerting
- Graphing
- Logging

**Retention Period** (good to know for exam)
- Data points < 60 seconds are available for 3 hours (high resolution)
- Data points = 60 seconds are available for 15 days
- Data points = 300 seconds (5 minutes) are available for 63 days
- Data points = 3600 (1 hour) are available for 445 days (15 months)

Note: shorter term data points are averaged to the next tier at the end of their lifecycle. e.g. a 1 second data point becomes a 1 minute average after 3 hours

### CloudWatch Metrics
Concepts:
- **Metrics**: A metric represents a time-ordered set of data points that are published to CloudWatch.
  - Metrics are uniquely defined by a name, a namespace, and zero or more dimensions. Each data point in a metric has a time stamp, and (optionally) a unit of measure.
  - Metrics exist only in the region in which they are created. Metrics cannot be deleted, but they automatically expire after 15 months if no new data is published to them.
- **Namespace**: A namespace is a container for CloudWatch metrics. AWS namespaces use the following naming convention: AWS/service.
- **Dimension**: A dimension is a name/value pair that is part of the identity of a metric. You can assign up to 10 dimensions to a metric.
  -  For example, you can get statistics for a specific EC2 instance by specifying the InstanceId dimension when you search for metrics.

### Cloudwatch Events
Concepts:
- **Events**: An event indicates a change in your AWS environment. AWS resources can generate events when their state changes.
  - e.g. Amazon EC2 generates an event when the state of an EC2 instance changes from pending to running
- **Targets**: A target processes events.
  - Targets can include:
    - Amazon EC2 instances
    - AWS Lambda functions
    - Kinesis streams
    - Amazon ECS tasks
    - Step Functions state machines
    - Amazon SNS topics
    - Amazon SQS queues
    - built-in targets
  - A target receives events in JSON format.
- **Rules**: A rule matches incoming events and routes them to targets for processing.
  - A single rule can route to multiple targets, all of which are processed in parallel.
  - Rules are not processed in a particular order.
  - A rule can customize the JSON sent to the target, by passing only certain parts or by overwriting it with a constant.

### CloudWatch Logs
Centralize the logs from all of your systems, applications, and AWS services that you use, in a single, highly scalable service.
- view logs
- search/query them for specific error codes or patterns
- filter them based on specific fields
- archive them securely for future analysis
- visualize log data in dashboards

#### Concepts:
- **Log Events**: A log event is a record of some activity recorded by the application or resource being monitored.
  - contains two properties: the timestamp of when the event occurred, and the raw event message. Event messages must be UTF-8 encoded.
- **Log Streams**: A log stream is a sequence of log events that share the same source.
  - intended to represent the sequence of events coming from the application instance or resource being monitored.
- **Log Groups**: Log groups define groups of log streams that share the same retention, monitoring, and access control settings.
  - Each log stream has to belong to one log group.

# X-Ray
- Collects data about requests that your application serves
- Provides tools you can use to view, filter, and gain insights into that data
- Identify issues and opportunities for optimization
