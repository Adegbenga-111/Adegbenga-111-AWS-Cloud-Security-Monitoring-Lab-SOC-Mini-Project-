# AWS Cloud Security Monitoring Lab (SOC Mini Project)

A hands-on AWS cloud security monitoring lab designed to simulate basic Security Operations Center (SOC) workflows using AWS native services.

This project demonstrates how to monitor cloud activity, detect suspicious behavior, collect EC2 authentication logs, and trigger automated alerts using CloudWatch and SNS.

---

# 🚀 Project Objectives

This lab was built to:

- Monitor AWS account activity
- Detect suspicious cloud events
- Monitor EC2 authentication logs
- Detect failed SSH login attempts
- Trigger automated email alerts
- Simulate real SOC monitoring workflows
- Gain hands-on cloud security experience

---

# 🛠️ AWS Services Used

| Service | Purpose |
|---|---|
| AWS CloudTrail | Captures AWS API activity |
| Amazon CloudWatch | Monitoring and alerting |
| CloudWatch Logs | Centralized log storage |
| CloudWatch Metric Filters | Detection rules |
| CloudWatch Alarms | Automated alerting |
| Amazon SNS | Email notifications |
| EC2 | Linux server monitoring |
| CloudWatch Agent | Ships EC2 logs to CloudWatch |

---

# 🧠 Architecture

```text
AWS Activity / EC2 Activity
        ↓
CloudTrail + EC2 Logs
        ↓
CloudWatch Log Groups
        ↓
Metric Filters
        ↓
Custom Metrics
        ↓
CloudWatch Alarms
        ↓
SNS Email Notifications
```

---

# 🔍 Security Detection Rules

## 1. AccessDenied Detection

Detects unauthorized AWS actions.

### Filter Pattern

```text
{ $.errorCode = "AccessDenied" }
```

### Metric

```text
SOCLab / AccessDeniedEvents
```

---

## 2. Root Account Usage Detection

Detects AWS root account activity.

### Filter Pattern

```text
{ $.userIdentity.type = "Root" }
```

### Metric

```text
SOCLab / RootAccountUsage
```

---

## 3. Failed SSH Login Detection

Detects failed SSH authentication attempts on EC2.

### Filter Pattern

```text
Failed password
```

### Metric

```text
SOCLab / FailedSSHLogins
```

---

# 🚨 CloudWatch Alarms

| Alarm | Purpose |
|---|---|
| AccessDeniedAlarm | Unauthorized AWS actions |
| RootUsageAlarm | Root account activity |
| FailedSSHLoginAlarm | Failed SSH login attempts |
| HighCPUUsageAlarm | High EC2 CPU usage |

---

# 📦 EC2 Log Monitoring Setup

## Install CloudWatch Agent

```bash
sudo apt update

wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb

sudo dpkg -i amazon-cloudwatch-agent.deb
```

---

## CloudWatch Agent Configuration

```json
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/auth.log",
            "log_group_name": "ec2-auth-logs",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```

---

## Start CloudWatch Agent

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
-s
```

---

# 🔐 IAM Configuration

An IAM role was attached to the EC2 instance using:

```text
CloudWatchAgentServerPolicy
```

This allowed the CloudWatch Agent to securely send logs to CloudWatch without hardcoded AWS credentials.

---

# 🧪 Testing Procedures

## Test Failed SSH Detection

Generate invalid SSH login attempts:

```bash
ssh wronguser@YOUR-EC2-IP
```

Expected Result:
- Failed login logged
- Metric increases
- Alarm triggers
- SNS email received

---

## Test CPU Monitoring

Install stress tool:

```bash
sudo apt install stress -y
```

Generate CPU load:

```bash
stress --cpu 2 --timeout 300
```

Expected Result:
- CPU exceeds threshold
- Alarm changes to ALARM state
- SNS email notification received

---

# ⚠️ Challenges Encountered

## CloudWatch Agent Authentication Failure

### Error

```text
NoCredentialProviders: no valid providers in chain
EC2RoleRequestError: no EC2 instance role found
```

### Root Cause

The EC2 instance had no IAM role attached.

### Solution

- Created IAM role
- Attached `CloudWatchAgentServerPolicy`
- Attached role to EC2 instance
- Restarted CloudWatch Agent

---

# 📈 Skills Demonstrated

- AWS Cloud Security
- SOC Monitoring
- CloudWatch Monitoring
- Detection Engineering
- EC2 Administration
- IAM Role Management
- Linux Log Monitoring
- Security Alerting
- Incident Detection
- Cloud Security Operations

---

# 🔮 Future Improvements

Potential future upgrades:

- AWS GuardDuty integration
- SIEM integration
- CloudWatch Dashboards
- Grafana dashboards
- Lambda auto-remediation
- Slack/Teams alerts
- Multi-account monitoring

---

# 📌 Key Learning Outcomes

This project provided hands-on experience with:

- Centralized logging
- Security event monitoring
- Metric filter creation
- Alert engineering
- EC2 authentication monitoring
- IAM troubleshooting
- Cloud security operations workflows

---

# 📧 Example Alert Workflow

```text
Failed SSH Login
        ↓
auth.log
        ↓
CloudWatch Agent
        ↓
CloudWatch Logs
        ↓
Metric Filter Match
        ↓
CloudWatch Alarm
        ↓
SNS Email Notification
```

---

# 🏁 Conclusion

This project demonstrates a practical AWS cloud monitoring and security detection environment capable of identifying suspicious AWS and Linux server activity using AWS native services.

The implementation simulates real-world SOC workflows involving:
- centralized logging
- detection engineering
- alerting
- monitoring
- incident detection

making it a strong foundational cloud security project.
