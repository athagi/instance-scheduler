# instance-scheduler
## words

### CloudFormation stack
* StartedTags: start したインスタンスにつけるタグ
* Stoppedtags: stop したインスタンスにつけるタグ
* Tagname: instance-schedulerの対象にするインスタンスのタグ（ex, Tagname=scheduler だったら EC2のタグがscheduler={利用するschedule} のものが対象になる。
* SchedulerFrequency: Lambda を実行する頻度（高いほどお金がかかる）

### Periods
何日の何時に起動して、終了するかを指定して、これのエイリアスを作成できる。
```
scheduler-cli create-period --begintime 10:00 --description "10:00 to 20:00" --endtime 20:00 --name day-time --weekdays mon-fri --region ap-northeast-1 --stack test-instance-scheduler
```

### Schedules
Period を束ねる設定ができる。このPeriod が動くタイムゾーンを設定できる。
```
scheduler-cli create-schedule


## usage


