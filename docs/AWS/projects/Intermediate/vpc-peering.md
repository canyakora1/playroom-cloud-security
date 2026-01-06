---
title: AWS - VPC peering
---

### :department_store: Fictitious Cloud Requirement Document
Company Overview

🔹 Company Name: NovaCart Technologies

🔹 Industry: Global E-commerce & Logistics

🔹 Company Size: ~1,200 employees

🔹 Regions of Operation: North America, Europe, Asia-Pacific

NovaCart is a rapidly growing e-commerce platform that supports millions of daily users. The company is migrating from an on-premises data center to AWS to improve scalability, security, and global availability.

### :fire_engine: Cloud Engineering Task

You are the Cloud Engineer responsible for designing the AWS network connectivity model.

Your Responsibilities:

`Consider VPC Peering for NovaCart’s use case`

Explain:

- How each option works

- Their advantages and limitations

- Impact on scalability, security, and operations

- Recommend one solution and justify why it best meets NovaCart’s requirements.

### VPC Peering
![vpc-peering](../../../assets/images/AWS/vpc-peering.png)

Check out the Official AWS Documentation for more indepth information on `VPC Peering`
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html" data-iframely-url="https://iframely.net/ssJrtKR2?theme=dark"></a></div></div><script async src="https://iframely.net/embed.js"></script>

To enable VPC peering, we would have to create two or more VPC (Virtual Private Clouds) in your AWS Account.

### Steps to create a VPC using the AWS Console

#### 📢 Step 1

Login to your AWS Account as an IAM User and go to the search menu. Search for `VPC`

#### 🔊 Step 2

On the VPV Dashboard glade. Click on `Create VPC`.


#### 📯 Step 3
- Select `VPC only` if not automatically selected.
- Fill in the `Name tag` option.
- Fill in the IPv4 CIDR (You can use those ones in the image above)
- Leave IPv6 CIDR block as `No IPv6 CIDR block`.
- Leave the `Tenancy` as `Default`.
- Leave the `VPC encryption vontrol($)` as __None__.
- Click `Create VPC` at the bottom right-hand corner for AWS to deploy the VPC.

Repeat same process as above for the second VPC, but be sure to change the `IPv4 CIDR` option. That is mandatory.

#### 🔔 Step 4
View the already created `VPC` in your `VPC Dashboard`.

![vpc-console](../../../assets/images/AWS/vpc-console.png)


### Creating a VPC via AWS CLI

!!! tip "**AWS VPC create**"

    ```bash
    aws ec2 create-vpc \
        --cidr-block 10.0.0.0/16 \
        --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=MyVPC}]'
    ```

Since we will be creating three (3) VPCs, I wouldn't expect you run this command three times. It is just cumbersome and tiring. Let's script this using `Bash`.

For simplicity, I will be creating three (3) VPC with incremental IPv4 CIDR like `10.0.0.0/16`, `10.1.0.0/16` and so on.

!!! tip "**Sample Bash Script**"

    ```bash
    #!/usr/bin/env bash

    set -euo pipefail

    # Set variables
    REGION="us-east-1"
    BASE_CIDR="10"
    INDEX=0

    VPC_NAMES=("prod-vpc" "staging-vpc" "dev-vpc")

    for VPC_NAME in "${VPC_NAMES[@]}"; do
    CIDR_BLOCK="$BASE_CIDR.$INDEX.0.0/16"

    echo "Creating VPC: $VPC_NAME ($CIDR_BLOCK)"

    VPC_ID=$(aws ec2 create-vpc \
        --region "$REGION" \
        --cidr-block "$CIDR_BLOCK" \
        --tag-specifications "ResourceType=vpc,Tags=[{Key=Name,Value=$VPC_NAME}]" \
        --query 'Vpc.VpcId' \
        --output text
    )

    echo "VPC created: $VPC_ID"

    echo "Enabling DNS support for $VPC_ID"
    aws ec2 modify-vpc-attribute \
        --region "$REGION" \
        --vpc-id "$VPC_ID" \
        --enable-dns-support

    echo "Enabling DNS hostnames for $VPC_ID"
    aws ec2 modify-vpc-attribute \
        --region "$REGION" \
        --vpc-id "$VPC_ID" \
        --enable-dns-hostnames

    ((INDEX++))
    done

    ```

