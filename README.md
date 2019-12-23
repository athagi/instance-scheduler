# instance-scheduler
this solution
https://docs.aws.amazon.com/solutions/latest/instance-scheduler/welcome.html

## outline
1. [CloudFormation Template](https://s3.amazonaws.com/solutions-reference/aws-instance-scheduler/latest/instance-scheduler.template) からリソースを作成する
2. `scheduler-cli` をインストールする
3. scheduler-cli から `periods` を作成する
4. scheduler-cli から period を結びつけた `schedule` を作成する
5. 対象のEC2インスタンス(RDSインスタンス) にタグをつけると `instance-scheduler` で指定されたスケジュールで起動停止が行われる

## words
### CloudFormation stack
* StartedTags: start したインスタンスにつけるタグ
* Stoppedtags: stop したインスタンスにつけるタグ
* Tagname: instance-schedulerの対象にするインスタンスのタグ（ex, Tagname=scheduler だったら EC2のタグがscheduler={利用するschedule} のものが対象になる。
* SchedulerFrequency: Lambda を実行する頻度（高いほどお金がかかる）

```
S3_BUCKET_NAME=s3_bucket_name
aws cloudformation deploy --template-file create-s3.yml --stack-name instance-scheduler-s3 --parameter-overrides S3BucketName=${S3_BUCKET_NAME}

aws cloudformation deploy --template-file instance-scheduler.json --stack-name instance-scheduler --parameter-overrides LogRetentionDays=30 DefaultTimezone=Asia/Tokyo StartedTags="started=true" StoppedTags="stopped=true" Regions=ap-northeast-1 CrossAccountRoles="" --s3-bucket ${S3_BUCKET_NAME} --capabilities CAPABILITY_IAM
```

### Periods
何日の何時に起動して、終了するかを指定して、これのエイリアスを作成できる。
```
scheduler-cli create-period --begintime 10:00 --description "10:00 to 20:00" --endtime 20:00 --name day-time --weekdays mon-fri --region ap-northeast-1 --stack test-instance-scheduler
```

### Schedules
Period を束ねる設定ができる。このPeriod が動くタイムゾーンを設定できる。
```
scheduler-cli create-schedule --timezone asia/tokyo --name test-schedule --periods test-period --stack test-instance-scheduler
```

### meke relationship 
EC2のタグにcFn作成時に指定した${Tagname}={対象としたいSchedule名} をつける。
schedule で指定された時間に起動し、停止するようになる。

```
aws ec2 crate-tags --resources ${EC2_INSTANCE_IDs} --tags Key=schedule,Value=test-schedule
```

then, this instance will run 10:00 - 20:00 on mon to fri

