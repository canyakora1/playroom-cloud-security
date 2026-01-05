---
date:
  created: 2025-12-28
  updated: 2026-01-04
authors:
  - dcyberguy
  - playroom
---

# AWS CLI Kung Fu


### __Guide: Installing the AWS CLI on your device__

- Use the official AWS install page at:
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html" data-iframely-url="https://iframely.net/hdhiFGMb?theme=dark"></a></div></div><script async src="https://iframely.net/embed.js"></script>

- Verify installtion by running:

```bash
aws --version
```

### AWS commands auto-complete (Optional)
- If using `zshrc` shell, run the below commands:

```bash
echo -e '\nexport PATH=/usr/local/bin/:$PATH\nautoload bashcompinit \
&& bashcompinit\nautoload -Uz compinit && compinit\ncomplete \
-C "/usr/local/bin/aws_completer" aws' >> ~/.zshrc
```

- If using `Bash` shell, run:

```bash
echo "complete -C '/usr/local/bin/aws_completer' aws" >> ~/.bashrc && source ~/.bashrc
```

- If using Windows: Follow the process below.

```powershell
# Open your $PROFILE file
Notepad $PROFILE

# If you don't have a $PROFILE, you can create one using;
if (!(Test-Path -Path $PROFILE ))
{ New-Item -Type File -Path $PROFILE -Force }

# Add the below code block to the $PROFILE and save.
Register-ArgumentCompleter -Native -CommandName aws -ScriptBlock {
    param($commandName, $wordToComplete, $cursorPosition)
        $env:COMP_LINE=$wordToComplete
        if ($env:COMP_LINE.Length -lt $cursorPosition){
            $env:COMP_LINE=$env:COMP_LINE + " "
        }
        $env:COMP_POINT=$cursorPosition
        aws_completer.exe | ForEach-Object {
            [System.Management.Automation.CompletionResult]::new($_, $_, 'ParameterValue', $_)
        }
        Remove-Item Env:\COMP_LINE     
        Remove-Item Env:\COMP_POINT  
}

```

<!-- more -->

### __Authenticating Using an IAM User's credentials for the AWS CLI__
Use the below AWS Official documentation for steps in configuring basic setting for an IAM user and retrieving the __Access Keys__.
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication-user.html" data-iframely-url="https://iframely.net/kj2yEH5s?theme=dark"></a></div></div><script async src="https://iframely.net/embed.js"></script>

Now that you have:

- The AWS Cli installed and configured
- Configured an IAM user and retrieved it's __Access Keys__.
- Configured the AWS Cli to use the __Access Keys__.

Next step; Developing your __AWS CLI Ninja skills__ :smile:

### __AWS CLI Ninja skills__
(Optional) Install the below tools to enhance your productivity.

- __`jq`__ --> JSON parsing
- __`bat`__ --> better output
- __`fzf`__ --> interactive selection
- __`aws-vault`__ --> credential management
- __`direnv`__ --> per-project AWS envs
```sh
sudo apt search direnv
Sorting... Done
Full Text Search... Done
direnv/noble-updates,noble-security 2.32.1-2ubuntu0.24.04.3 amd64
Utility to set directory specific environment variables
```

Install using
```sh
sudo apt install jq bat fzf direnv
```

#### Core AWS CLI flags you should be familiar with

:large_orange_diamond: __`--profile`__

`Use in multiple account scenerio.`
```bash
aws s3 ls --profile dev
aws ec2 describe-instances --profile prod
```

`Set a default profile for a session:`
```bash
export AWS_PROFILE=dev
```

:large_orange_diamond: __`--region`__

`Never assume a region.`
```bash
aws ec2 describe-instances --region us-east-1
```

`Set it once:`
```bash
export AWS_DEFAULT_REGION=us-east-1
```

:large_orange_diamond: __`--output`__

`Change output format.`
```bash
aws ec2 describe-instances --output table
aws ec2 describe-instances --output yaml
```

:large_orange_diamond: __`--query`__ (JMESPath = built-in power)

`Extract exactly what you want without jq.`
```bash
aws ec2 describe-instances \
  --query 'Reservations[].Instances[].InstanceId'
```
`Filter running instances only:`
```bash
aws ec2 describe-instances \
  --query "Reservations[].Instances[?State.Name=='running'].InstanceId"
```


#### AWS CLI + jq (Next-Level Power)

:fire: `Extract Instance IDs`
```bash
aws ec2 describe-instances | jq -r '.Reservations[].Instances[].InstanceId'
```

:fire: `Get public IPs of running instances
```bash
aws ec2 describe-instances \
| jq -r '.Reservations[].Instances[]
| select(.State.Name=="running")
| .PublicIpAddress'
```

:fire: `List EC2 Name + Instance ID`
```bash
aws ec2 describe-instances \
| jq -r '.Reservations[].Instances[]
| [.InstanceId,
   (.Tags[]? | select(.Key=="Name") | .Value)] | @tsv'
```


#### AWS CLI "NINJA" Productivity Tricks

:rocket: `Dry run (simulate before you destroy things)`
```bash
aws ec2 terminate-instances --instance-ids <<instance-id>> --dry-run
```

:rocket: `Pipe into xargs (bulk actions)`
```bash
aws ec2 describe-instances \
  --query "Reservations[].Instances[?State.Name=='running'].InstanceId" \
  --output text \
| xargs aws ec2 stop-instances
```

:rocket: `Use --no-paginate`

`Avoid annoying pagers.`
```bash
aws ec2 describe-instances --no-paginate
```

:rocket: `Disable the pager permanently`
```bash
export AWS_PAGER=""
```
`(Add to .bashrc)`


#### S3 Power Moves

:bucket: `Sync directories`
```bash
aws s3 sync . s3://my-bucket
aws s3 sync s3://my-bucket ./local-dir
```

:bucket: `Human-readable bucket sizes`
```bash
aws s3 ls s3://terraform-s3-remote-backend-playroom --recursive --human-readable --summarize
2024-11-01 16:14:06  181 Bytes dev/terraform.state

Total Objects: 1
   Total Size: 181 Bytes
```

:bucket: `Find large objects`
```bash
aws s3 ls s3://my-bucket --recursive \
| sort -k3 -h \
| tail -10
```


#### IAM & Security Tricks (Very Important)

:closed_lock_with_key: `Who am I?`
```bash
aws sts get-caller-identity
```

:closed_lock_with_key: `List IAM policies attached to a role`
```bash
aws iam list-attached-role-policies --role-name MyRole
```

:closed_lock_with_key: `Simulate IAM permissions`
```bash
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::123456789012:role/MyRole \
  --action-names s3:PutObject
```

#### `CloudWatch & Logs Like a Pro`

:mag_right: `Tail logs in real-time`
```bash
aws logs tail /aws/lambda/my-function --follow
```

:mag_right: `Search logs`

```bash
aws logs filter-log-events \
  --log-group-name /aws/lambda/my-function \
  --filter-pattern "ERROR"
```


#### `EC2 Metadata (From Inside an Instance)`

```bash
curl http://169.254.169.254/latest/meta-data/
```

`Instance ID:`

```bash
curl http://169.254.169.254/latest/meta-data/instance-id
```


#### `AWS CLI Aliases (Huge Time Saver)`

`Add to ~/.bashrc:`

```bash
alias awsls='aws s3 ls'
alias awswhoami='aws sts get-caller-identity'
alias ec2running='aws ec2 describe-instances --query "Reservations[].Instances[?State.Name==\`running\`].InstanceId" --output text'
```

`Reload:`

```bashrc
source ~/.bashrc
```


#### `Use aws configure sso (Modern Auth)`

```bash
aws configure sso
```

`Then:`

```bash
aws s3 ls --profile my-sso-profile
```


#### `Debugging AWS CLI Issues`

:tools: `Enable debug mode`

```bash
aws ec2 describe-instances --debug
```
:tools: `Check config`

```
aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ****************M7PW shared-credentials-file
secret_key     ****************nC5J shared-credentials-file
    region                us-east-1      config-file    ~/.aws/config
```

#### `Automation & Scripting Patterns`

`Bash loop`
```bash
for r in us-east-1 us-west-2 eu-west-1; do
  aws ec2 describe-instances --region $r --query 'Reservations[].Instances[].InstanceId'
done
```

#### `AWS CLI Mental Model (Important)`

`Think in pipes:`

```bash
aws → filter → transform → act
```


`Example:`

```bash
aws ec2 describe-instances \
| jq ... \
| xargs aws ec2 stop-instances
```


























