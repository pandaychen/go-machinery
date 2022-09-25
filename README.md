# Go Machinery example

## Table of Contents

- [About](#about)
- [Getting Started](#getting_started)
- [Usage](#usage)

## About <a name = "about"></a>

A very basic example of task queuing in Machinery and Golang. In the example a golang web server takes input as task and worker consume that task.

## Getting Started <a name = "getting_started"></a>

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See [deployment](#deployment) for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install the software and how to install them.

```
go environment
```

### Usage <a name = "usage"></a>

A step by step series of examples that tell you how to get a development env running.

start with dependencies download

```
go mod download
```

Spawn a redis server for `Broker` and `ResultBackend`

```
docker run -d -p 6379:6379 redis
```

run the webserver server on port 5000

```
go run main.go server
```

启动worker
```
[root@VM_120_245_centos ~/go-machinery]# ./main worker
```


触发任务生成：
```
[root@VM_120_245_centos ~]# curl --location --request POST 'localhost:5000/send_email' --header 'Content-Type: application/json' --data '{"email": "anyone@gmail.com","subject": "Hello","body": "World"}'
{"task_uuid":"task_07a70caa-87b8-4659-8fb0-577240983789"}
```

worker输出：
```text
{"level":"info","ts":1664101809.7196665,"caller":"utils/utils.go:19","msg":"initing task server"}
{"level":"info","ts":1664101809.7198565,"caller":"go-machinery/main.go:52","msg":"worker"}
INFO: 2022/09/25 18:30:09 worker.go:57 Launching a worker with the following settings:
INFO: 2022/09/25 18:30:09 worker.go:58 - Broker: redis://localhost:6379
INFO: 2022/09/25 18:30:09 worker.go:60 - DefaultQueue: 
INFO: 2022/09/25 18:30:09 worker.go:64 - ResultBackend: redis://localhost:6379
INFO: 2022/09/25 18:30:09 redis.go:96 [*] Waiting for messages. To exit press CTRL+C
DEBUG: 2022/09/25 18:30:11 redis.go:330 Received new message: {"UUID":"task_07a70caa-87b8-4659-8fb0-577240983789","Name":"send_email","RoutingKey":"","ETA":null,"GroupUUID":"","GroupTaskCount":0,"Args":[{"Name":"","Type":"string","Value":"eyJlbWFpbCI6ImFueW9uZUBnbWFpbC5jb20iLCJzdWJqZWN0IjoiSGVsbG8iLCJib2R5IjoiV29ybGQifQ=="}],"Headers":{},"Priority":0,"Immutable":false,"RetryCount":0,"RetryTimeout":0,"OnSuccess":null,"OnError":null,"ChordCallback":null,"BrokerMessageGroupId":"","SQSReceiptHandle":"","StopTaskDeletionOnError":false,"IgnoreWhenTaskNotRegistered":false}
2022/09/25 18:30:13 smtp error: 535 5.7.8 Username and Password not accepted. Learn more at
5.7.8  https://support.google.com/mail/?p=BadCredentials q9-20020a170902a3c900b00177e590df96sm9023487plb.118 - gsmtp
DEBUG: 2022/09/25 18:30:13 worker.go:263 Processed task task_07a70caa-87b8-4659-8fb0-577240983789. Results = false
```
