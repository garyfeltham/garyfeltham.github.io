How to Disable AWS Cloudwatch Alarms for a Maintenance Window
===

If you are like me and have a large number of alarms configured in Cloudwatch that trigger when you run any planned maintenance. There is a command line way to disable the alarms from actioning. 

Currently this is not available via the Cloudwatch panel however an ActionsEnabled flag can be configured via the Cloudwatch API. [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-cw-alarm.html]()

To perform this on a console prior to running your maintenance window a single command can disable all alarms from actioning.

Note you will need `aws cli`, `jq` and `tr` to be installed.

Some caveats:

- this will disable all alarms and it is non selective. 
- the command is for a single region based on the configured region in your credentials file

## Disable Alarm Actions
```bash
aws cloudwatch --profile production describe-alarms | \
jq '.MetricAlarms | .[].AlarmName' | \
tr '\n' ' ' | \
xargs aws cloudwatch --profile production disable-alarm-actions --alarm-names
```

## Enable Alarm Actions
```bash
aws cloudwatch --profile production describe-alarms | \
jq '.MetricAlarms | .[].AlarmName' | \
tr '\n' ' ' | \
xargs aws cloudwatch --profile production enable-alarm-actions --alarm-names
```

## Check on the State of Alarm Actions
```bash
aws cloudwatch --profile production describe-alarms | \
jq -r '.MetricAlarms[] | "\(.AlarmName): \(.ActionsEnabled)"'
```

