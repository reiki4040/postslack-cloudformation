Post Slack CloudFormation
===
API Gateway + Lambda CloudFormation template sample.

build below stack.
```
API Gateway(with api key) -> Lambda(node.js) -> Slack(incoming webhook)
```

# How to use

- Create Slack Webhook URL
- Run CloudFormation
- Post test

## Create Slack Webhook URL

create Slack incoming webhook
https://api.slack.com/incoming-webhooks

overwrite dummy value `none` SLACK_WEBHOOK_URL variable in CloudFormation template.
```
Environment: {
  Variables: {SLACK_WEBHOOK_URL:none}
}
```

## Run CloudFormation

run CloudFormation
```
aws cloudformation create-stack --stack-name PostSlackCfn --template-body file://apigateway.json --region ap-northeast-1 --capabilities CAPABILITY_IAM
```

`--stack-name` and `--region` are able to replace to your env.

## Post test

show value below from CloudFormation Output.
```
aws cloudformation describe-stacks --stack-name PostSlackCfn
```

show like below. get <api key id> and <post url>
```
{
    "Stacks": [
        {
            "StackId": "arn:aws:cloudformation:xxxxxxxxxxxxxxx",
            "LastUpdatedTime": "2018-02-11T07:31:34.072Z",
            "Tags": [],
            "Outputs": [
                {
                    "Description": "api key",
                    "OutputKey": "ApiKey",
                    "OutputValue": "<your api key id>"
                },
                {
                    "Description": "post url",
                    "OutputKey": "PostUrl",
                    "OutputValue": "<your post url>"
                }
            ],
            "CreationTime": "2018-02-11T05:15:58.564Z",
            "Capabilities": [
                "CAPABILITY_IAM"
            ],
            "StackName": "PostSlackCfn",
            "NotificationARNs": [],
            "StackStatus": "UPDATE_COMPLETE",
            "DisableRollback": false
        }
    ]
}
```

get api key value
```
aws apigateway get-api-key --api-key <your api key id> --include-value
```

show like below. get <your api key>
```
{
    "description": "CloudFormation API Key V1",
    "enabled": true,
    "value": "<your api key>",
    "stageKeys": [],
    "lastUpdatedDate": 1518326162,
    "createdDate": 1518326162,
    "id": "<your api key id>",
    "name": "PostSlackCfnApiKey"
}
```

test with curl
```
curl -XPOST <your post url> -H "x-api-key: <your api key>" -H 'Content-Type: application/json' -d '{"message":"Hi"}'
```

show `posted to slack` is OK and show message 'Hi' in your Slack.
if failed then check CloudWatch log.

