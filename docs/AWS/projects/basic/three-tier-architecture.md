---
title: AWS - Deploy a three tier AWS Architecture
---

# AWS - Deploy a three tier AWS Architecture

![aws-3-tier](../../../../.gitbook/assets/aws-3-tier.png)

Check our our Github page for more helpful tips and tricks plus and detailed walkthrough from start to finish

### :material-server-network: How to deploy a Three Tier AWS VPC Architecture and it's components.

#### :fontawesome-solid-cubes-stacked: Prerequisites

* You need to have an AWS account. Click here to get your [AWS free tier account](https://signin.aws.amazon.com/signup?request_type=register) for up to six (6) months.
* No coding experience
* No previous IT knowledge

#### 🔹 Tier Breakdown

| Tier        | Subnet Type    | Components                                   |
| ----------- | -------------- | -------------------------------------------- |
| Public Tier | Public Subnet  | EC2 Instances, Auto Scaling Group, S3 Bucket |
| App Tier    | Private Subnet | API Gateway, Lambda Functions                |
| DB Tier     | Private Subnet | RDS or EC2-based database                    |

#### 🧱 Infrastructure Components:

* VPC with CIDR block 10.0.0.0/16
* 2 Availability Zones for high availability
* Public Subnet with IGW and Auto Scaling Group
* Private Subnets with API Gateway, Lambda Functions, and RDS or EC2-based database
* Security Groups for tier isolation and controlled access
* Route Tables for subnet-level routing

#### 📢 Step 1

* If you haven't already, sign up for an AWS account [here](https://aws.amazon.com/free/).
* Use the above Github link to follow the step by step instructions on how to deploy a three tier architecture on AWS.

#### 🔊 Step 2

#### 📯 Step 3

#### 🔔 Step 4
