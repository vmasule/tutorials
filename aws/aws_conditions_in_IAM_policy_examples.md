### Restrict EC2 to region only - This way any AWS resource can be restricted. 

```
 "Condition": {
                "StringEquals": {
                    "ec2:Region": "us-east-1"
                }
            },
```

### Restrict EC2 Instance Type

```
"Condition": {
                "StringEquals": {
                    "ec2:InstanceType": [
                        "m4.10xlarge",
                        "m4.16xlarge"
                    ]
                }
            }
```

