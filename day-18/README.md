# Amazon EventBridge Overview

**Amazon EventBridge is a fully managed serverless event bus service that makes it easy to connect applications together using data from your own applications, integrated SaaS applications, and AWS services.** It allows you to build event-driven architectures by routing events to targets like AWS Lambda functions, SNS topics, and more.

**Example of Usage:**

Here’s a simple example of how you might use EventBridge to trigger a Lambda function:

1. **Create a Rule**: Define a rule that matches events of interest.
2. **Add a Target: Specify the target (e.g., a Lambda function) that should be triggered when the rule matches an event.**
3. **Deploy and Test**: Deploy your setup and test it by sending an event to see if the Lambda function is triggered.

**Basic Example Using AWS CLI:**

```bash
# Create a rule that triggers a Lambda function
aws events put-rule --name MyRule --event-pattern '{"source":["aws.ec2"]}'
aws events put-targets --rule MyRule --targets "Id"="MyLambdaTarget","Arn"="arn:aws:lambda:us-east-1:123456789012:function:MyFunction"
```



---
### AWS Cloud Cost Optimization Example - Identifying Stale Resources

**Identifying Stale EBS Snapshots:**

In this example, we'll **create a Lambda function that identifies EBS snapshots that are no longer associated with any active EC2 instance and deletes them to save on storage costs.**

**Description:**

The Lambda function fetches all EBS snapshots owned by the same account ('self') and also retrieves a list of active EC2 instances (running and stopped). For each snapshot, it checks if the associated volume (if exists) is not associated with any active instance. If it finds a stale snapshot, it deletes it, effectively optimizing storage costs.

[Lambda Src Code](./ebs_stale_snapshosts.py)

---
### EBS

**Amazon EBS (Elastic Block Store) in AWS is like your cloud-based storage buddy. It provides block-level storage volumes for use with Amazon EC2 instances.** In simpler terms, it's where you can store data that your virtual machines can access **like a physical hard drive, but in the cloud.** It’s great for situations where you need persistent, durable, and high-performance storage for databases, applications, and other workloads. It’s also flexible in terms of resizing volumes and changing performance metrics on-the-fly, so you're never really stuck if your storage needs change.



---