# GrindLine — AWS Cloud Project

## About the Project

**GrindLine** is a cloud-native fitness tracking web application deployed on Amazon Web Services (AWS).

The project focuses on deploying a Node.js application in a scalable and monitored AWS environment while implementing secure secret management, load balancing, auto scaling, monitoring, and scheduled notifications.

---

## What is GrindLine?

GrindLine is a fitness tracking application designed to help users manage their fitness and workout activities through a web application.

The application provides:

- User registration and login
- Authentication using JWT
- Fitness/workout tracking
- PostgreSQL database integration
- Cloud deployment on AWS

The main focus of this project is not only the application itself, but also how the application is deployed, secured, monitored and scaled using AWS services.

---

## What I Built

I deployed the GrindLine application on AWS and created the supporting cloud infrastructure.

The project includes:

- Custom AWS VPC and subnets
- EC2-based application deployment
- PostgreSQL database using Amazon RDS
- IAM role for EC2
- Secure secret management using AWS Systems Manager Parameter Store
- Application Load Balancer
- Auto Scaling Group
- CloudWatch application logging
- CloudWatch CPU monitoring and alarm
- EventBridge scheduled automation
- Lambda-based reminder processing
- SNS notification system

---

## Architecture

```text
                         INTERNET
                            |
                            v
                  +-------------------+
                  | Application       |
                  | Load Balancer     |
                  | HTTP :80          |
                  +---------+---------+
                            |
                            v
                  +-------------------+
                  | EC2               |
                  | Node.js + Express  |
                  | PM2 :3000         |
                  +---------+---------+
                            |
                            v
                  +-------------------+
                  | RDS PostgreSQL    |
                  +-------------------+


        +--------------------------------------+
        |              EC2 IAM Role            |
        +-------------------+------------------+
                            |
                            v
                  +-------------------+
                  | SSM Parameter     |
                  | Store             |
                  +-------------------+
                    |               |
                    v               v
              DB Password       JWT Secret


        EC2
         |
         v
   CloudWatch Agent
         |
         +--------------------+
         |                    |
         v                    v
   Application Logs       CPU Alarm


   EventBridge Scheduler
            |
            v
         Lambda
            |
            v
           SNS
            |
            v
          Email
```

---

## AWS Services Used

| AWS Service | Purpose |
|---|---|
| Amazon VPC | Isolated network environment |
| Amazon EC2 | Runs the GrindLine application |
| Amazon RDS | Hosts the PostgreSQL database |
| IAM | Controls AWS permissions |
| SSM Parameter Store | Stores sensitive application secrets |
| Application Load Balancer | Distributes incoming HTTP traffic |
| Auto Scaling Group | Manages EC2 capacity |
| Amazon CloudWatch | Application logs and monitoring |
| EventBridge Scheduler | Runs scheduled automation |
| AWS Lambda | Processes scheduled reminders |
| Amazon SNS | Sends notification messages |

---

## Application Flow

When a user accesses GrindLine:

```text
User
  |
  v
Application Load Balancer
  |
  v
EC2 Instance
  |
  v
Node.js / Express Application
  |
  v
RDS PostgreSQL
```

The Application Load Balancer provides a single entry point for the application.

The Node.js application runs on EC2 using PM2.

Application data is stored in PostgreSQL running on Amazon RDS.

---

## Security

Security was implemented using AWS IAM and Systems Manager Parameter Store.

Sensitive values such as:

- Database password
- JWT secret

are stored in SSM Parameter Store instead of being hardcoded in the application.

The EC2 instance uses an IAM role to access the required AWS resources.

Secret values are not stored in this GitHub repository.

---

## Secret Management

The application uses the following SSM parameters:

```text
/grindline/DB_PASSWORD
/grindline/JWT_SECRET
```

The application retrieves these values at startup using the AWS SDK.

The actual parameter values are never stored in the repository.

---

## Monitoring

Amazon CloudWatch is used to monitor the application.

The CloudWatch Agent collects PM2 application logs:

```text
grindline-out.log
grindline-error.log
```

These logs are sent to:

```text
/grindline/application
```

A CloudWatch CPU alarm is also configured for the EC2 instance.

---

## Load Balancing

An Application Load Balancer named:

```text
GrindLine-ALB
```

is used as the entry point for incoming HTTP traffic.

The ALB forwards traffic to the GrindLine target group.

The target group forwards requests to the Node.js application running on port `3000`.

The health check endpoint is:

```text
/api/health
```

---

## Auto Scaling

The application is connected to an Auto Scaling Group:

```text
GrindLine-ASG
```

Configured capacity:

```text
Minimum: 1
Desired: 1
Maximum: 2
```

The Auto Scaling Group uses a launch template and is connected to the Application Load Balancer target group.

This allows the application infrastructure to scale within the configured capacity.

---

## Scheduled Notifications

GrindLine also includes a scheduled notification workflow.

```text
EventBridge Scheduler
        |
        v
      Lambda
        |
        v
       SNS
        |
        v
      Email
```

EventBridge Scheduler is configured to invoke the Lambda function on a daily schedule.

The Lambda function publishes a reminder message to an SNS topic.

The SNS topic can then deliver the notification to the configured subscription.

---

## Project Structure

```text
GrindLine-AWS-Cloud-Project/
│
├── README.md
│
├── architecture/
│   └── grindline-architecture.png
│
└── screenshots/
    ├── 01-application.png
    ├── 02-vpc.png
    ├── 03-ec2.png
    ├── 04-rds.png
    ├── 05-iam-role.png
    ├── 06-ssm.png
    ├── 07-cloudwatch-logs.png
    ├── 08-cloudwatch-alarm.png
    ├── 09-load-balancer.png
    ├── 10-target-group.png
    ├── 11-auto-scaling.png
    ├── 12-lambda.png
    ├── 13-eventbridge.png
    └── 14-sns.png
