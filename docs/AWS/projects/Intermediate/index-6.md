---
title: Centralized Logging & Alerting System in AWS
---

## Operations & Observability
__Focus__: Keeping the "lights on" through monitoring and automated recovery.

__The Project:__ Build a Centralized Logging & Alerting System.

__Key Tasks:__

- Use CloudWatch Logs and VPC Flow Logs to monitor traffic.

- Set up CloudWatch Alarms that trigger SNS notifications (e.g., Slack/Email) when CPU or 4xx errors spike.

- Implement AWS Config to monitor for non-compliant resources (e.g., unencrypted S3 buckets).

- Advanced Twist: Use AWS Systems Manager (SSM) to patch EC2 instances automatically without SSH.