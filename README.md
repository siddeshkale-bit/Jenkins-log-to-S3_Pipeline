Automated Log File Monitoring & S3 Upload Using Jenkins Pipeline

This project automates the monitoring, uploading, and cleanup of large log files using a combination of:

Jenkins Pipeline (declarative)

AWS S3

Shell script (log size checker)

Cron job (scheduled execution)

Email notifications (Gmail SMTP)

The system continuously monitors an application log file.
If the log file grows beyond 1GB, Jenkins automatically:

Uploads the log file to AWS S3

Clears the original log file

Sends an email notification with job results

Maintains an audit trail in S3

Project Architecture
[ Cron Job ]  ---- triggers ---->  [ Shell Script ]
                               |
                               V
                         [ Jenkins API ]
                               |
                               V
                  [ Jenkins Pipeline Execution ]
                               |
                               +--> Upload log to S3
                               +--> Clear log file
                               +--> Send email notification

Features Implemented
1. Automatic Log Size Monitoring

A shell script continuously checks the access log size:

File monitored: /var/log/apache2/access.log

Threshold: 1GB

When threshold exceeds → Jenkins job triggers automatically

2. Jenkins Pipeline Automation

Jenkins pipeline performs:

File size validation

Conditional S3 upload

Log file truncation

Email notification on completion

3. AWS S3 Storage Integration

Logs are archived in S3 for long-term analysis.

Example S3 path:

s3://your-log-bucket/access-logs/

4. Email Notifications

SMTP: Gmail

HTML-formatted email

Includes job status, file size, S3 URL, and console link

5. Cron Job Scheduling

Cron triggers the monitoring script every 5 minutes.

Example cron entry:

*/5 * * * * /opt/scripts/monitor_log_size.sh

Repository Structure
├── Jenkinsfile                 # Full Jenkins pipeline script
├── monitor_log_size.sh         # Script that checks log size & triggers Jenkins
├── Images/                     # Screenshots for documentation
│     ├── Screenshot-1.png
│     ├── Screenshot-2.png
│     └── ...
└── README.md                   # Project documentation

Technologies Used
Component	Technology
CI/CD Automation	Jenkins Pipeline
Cloud Storage	AWS S3
OS	Ubuntu / Amazon Linux
Script	Bash
Scheduling	Cron
Email	Gmail SMTP + Jenkins Email Extension Plugin
How the Pipeline Works
Step 1 — Check File Size

Jenkins reads the log file size:

def size = sh(script: "stat -c%s ${params.LOG_FILE_PATH}", returnStdout: true).trim()

Step 2 — Conditional Upload
when { environment name: 'UPLOAD_NEEDED', value: 'true' }

Step 3 — Upload to S3
aws s3 cp /var/log/apache2/access.log s3://your-bucket/access-logs/

Step 4 — Clear Log File
sudo truncate -s 0 /var/log/apache2/access.log

Step 5 — Send Email Notification

HTML template includes:

Build status

Log file processed

File size

S3 upload path

Console URL

Cron Job Setup

Edit cron:

sudo crontab -e


Add entry:

*/5 * * * * /opt/scripts/monitor_log_size.sh


This ensures checks run automatically every 5 minutes.

Jenkins Trigger Script (monitor_log_size.sh)
#!/bin/bash

LOG_FILE="/var/log/apache2/access.log"
MAX_SIZE=$((1024 * 1024 * 1024))  # 1GB

SIZE=$(stat -c%s "$LOG_FILE")

if [ "$SIZE" -gt "$MAX_SIZE" ]; then
    curl -X POST "http://<JENKINS_URL>/job/log-file-to-s3/build?token=trigger"
fi

Full Jenkins Pipeline (Jenkinsfile)

The pipeline includes:

Parameterized log file path

Conditional upload

Amazon S3 upload

Log cleanup

Email notification

(Your exact Jenkins code goes here.)

Screenshots (for Documentation)

Include the following key screenshots in the /Images folder:

EC2 instance running

Apache access log location

S3 bucket file uploads

Jenkins pipeline configuration

Jenkinsfile script

Cron job entry

Email notification received

Jenkins console output

AWS CLI output showing uploaded logs

Permissions fixes (sudoers file)

Plugin installation view

Conclusion

This project demonstrates:

Real-world CI/CD automation

Cloud-based log archival system

Jenkins pipeline mastery

Shell scripting

Cron automation

Email alerting for production systems

It is suitable for DevOps internships, cloud automation portfolios, and CI/CD engineering roles.

Author

Siddesh Kale
Email: siddeshkale25aug@gmail.com

Cloud & DevOps | Automation | CI/CD |
