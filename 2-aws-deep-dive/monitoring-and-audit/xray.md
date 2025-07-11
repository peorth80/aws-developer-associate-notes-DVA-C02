# AWS X-Ray

- The classic way to debug in Production:
    - Test locally
    - Add log statements everywhere
    - Re-deploy in production
- Log formats differ across applications using CloudWatch and analytics is hard.
- Debugging: monolith is easy, distributed services can get really hard
- No common views of your entire architecture!
- AWS X-Ray helps solve these problems

### Advantages of X-Ray
- Troubleshooting performance (bottlenecks)
- Understand dependencies in a microservice architecture
- Pinpoint service issues
- Review request behavior
- Find errors and exceptions
- Are we meeting time SLA?
- Where I am throttled?
- Identify users that are impacted

### Compatibility
- AWS Lambda
- Elastic Beanstalk
    - add xray-daemon.config file to `.ebextensions/` folder in your code
- ECS
- ELB
- API Gateway
- EC2 Instances or any application server (even on premise)

### X-Ray leverages Tracing
- **Tracing** is an end to end way to following a “request”
- Each component dealing with the request adds its own “trace”
- Tracing is made of segments (+ sub segments -- when you need more granularity)
- Annotations can be added to trace to provide extra-information
- Ability to trace:
    - Every request
    - Sample request (as a % for example or a rate per minute)
- X-Ray Security:
    - IAM for authorization
    - KMS for encryption at rest

### How to enable X-Ray
1. Your code (Java, Python, Go, Node.js, .NET) must import the AWS X-Ray SDK
    - Very little code modification needed
    - The application SDK will then capture:
        - Calls to AWS services
        - HTTP / HTTPS requests
        - Database Calls (MySQL, PostgreSQL, DynamoDB)
        - Queue calls (SQS)
2. Install the X-Ray daemon (EC2) or enable X-Ray AWS Integration (Lambda)
    - X-Ray daemon works as a low level UDP packet interceptor (Linux / Windows / Mac...)
    - AWS Lambda / other AWS services already run the X-Ray daemon for you
    - Each application must have the IAM rights to write data to X-Ray

### X-Ray's internal magic
- X-Ray service collects data from all the different services
- Service map is computed from all the segments and traces
- X-Ray is graphical, so even non-technical people can help troubleshoot
- X-Ray has a way to filter expressions, and this is supported by adding _annotations_ (in key/value format, up to 50 per trace)
- You can also add metadata to the traces, but this does not allow indexing. You can store whatever, even objects or lists.
- X-Ray Headers:
    - `X_AMNZ_TRACE_ID`: tracing header
    - `AWS_XRAY_CONTEXT_MISSING`: LOG_ERROR by default (logs error and continue execution)
    - `AWS_XRAY_DAEMON_ADDRESS`: IPADDRESS:PORT to define where the X-Ray Daemon is installed 

### AWS X-Ray Troubleshooting
- If X-Ray is not working on EC2:
    - Ensure the EC2 IAM Role has the proper permissions
    - Ensure the EC2 instance is running the X-Ray Daemon
- To enable on AWS Lambda:
    - Ensure it has an IAM execution role with proper policy (AWSX-RayWriteOnlyAccess)
    - Ensure that X-Ray is imported in the code
