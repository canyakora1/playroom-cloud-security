

# Flaws.cloud Level 1

Status: Done
Assign: Dcyberguy

![image.png](../../../assets/images/Flaws/level-1-banner.png)

```bash
aws s3 ls s3://flaws.cloud --no-sign-request
2017-03-13 23:00:38       2575 hint1.html
2017-03-02 23:05:17       1707 hint2.html
2017-03-02 23:05:11       1101 hint3.html
2024-02-21 21:32:41       2861 index.html
2018-07-10 12:47:16      15979 logo.png
2017-02-26 20:59:28         46 robots.txt
2017-02-26 20:59:30       1051 secret-dd02c7c.html

```

### Download the Secret file

```bash
aws s3 cp s3://flaws.cloud/secret-dd02c7c.html .  --no-sign-request
download: s3://flaws.cloud/secret-dd02c7c.html to ./secret-dd02c7c.html
❯ ls
       secret-dd02c7c.html 
```

```bash
<html>
   2   │     <head>
   3   │         <title>flAWS</title>
   4   │         <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">
   5   │         <style>
   6   │             body { font-family: Andale Mono, monospace; }
   7   │             :not(center) > pre { background-color: #202020; padding: 4px; border-radius: 5px; border-color:#00d000; 
   8   │             border-width: 1px; border-style: solid;} 
   9   │         </style>
  10   │     </head>
  11   │ <body 
  12   │   text="#00d000" 
  13   │   bgcolor="#000000"  
  14   │   style="max-width:800px; margin-left:auto ;margin-right:auto"
  15   │   vlink="#00ff00" link="#00ff00">
  16   │     
  17   │ <center>
  18   │ <pre >
  19   │  _____  _       ____  __    __  _____
  20   │ |     || |     /    ||  |__|  |/ ___/
  21   │ |   __|| |    |  o  ||  |  |  (   \_ 
  22   │ |  |_  | |___ |     ||  |  |  |\__  |
  23   │ |   _] |     ||  _  ||  `  '  |/  \ |
  24   │ |  |   |     ||  |  | \      / \    |
  25   │ |__|   |_____||__|__|  \_/\_/   \___|
  26   │ </pre>
  27   │ 
  28   │ <h1>Congrats! You found the secret file!</h1>
  29   │ </center>
  30   │ 
  31   │ 
  32   │ Level 2 is at <a href="http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud">http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud</a>

```