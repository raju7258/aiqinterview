# Challenge
## Implementing a microservices architecture

## Frontend Tier

In this use case we are assuming that the application to be hosted are in a 3-tier application. The front-end of the application will be hosted on AWS S3 which is highly reliable as it provides high reliability by replicating the data across multiple Availability Zones furthermore, we can have a DR backup of the front application in another region by using cross region replication. This bucket can be integrated with CloudFront to serve the frontend through edge location reducing latency and helps cache the content the edge location. AWS Cloudfront provides orgin failover feature that allows us to have multiple origins. Incase primary origin fails we can serve the frontend from the secondary S3 Bucket which is being replicated in another region this not only provides high availability but also provide a DR solution for the frontend. We can further safeguard our frontend assets using versioning.

## Application Tier (Middle Tier)

We can host the application tier either in Fargate or EKS on AWS 
For the application to be hosted in either of the container platforms we need to ensure our application is containerized, once the application is containerized, we can decide to opt for either EKS or Fargate depending on the use case.  Fargate is a serverless platform that enables us to run containers without managing the underlying instances or the os. This solution is recommended for microservices that can run independently without any dependency and transient in nature. Fargate allows us to have a highly scalable and resilient infrastructure managed by AWS. You can run multiple tasks across different AZ’s ensuring high availability. Moreover, by using an AWS managed ALB we can have path based routing or host based routing sending the traffic to required services running inside AWS Fargate Cluster. Having an ALB helps us also load balance the traffic as well as offload SSL connections on ALB resulting in few CPU cycles on the Fargate service.

## Database Tier

In order to have a decoupled architecture without a single point of failure it is recommended that we avoid the heavy lifting and use AWS managed DB such as RDS .RDS DB provides a cluster with multiple Read Replicas and Multi-AZ for high availability and reliability. We can have automated snapshots or backups of the DB export to S3 if needed. The DB scales up when required during maintenance window as Scaling Up requires downtime. The storage can also be auto scaled automatically based on the threshold set.

## Additional Inputs
We should enable logging at all layers such as infrastructure layer and application.
### Monitoring and Loggging
- #### FrontEnd Tier
    In order to monitor frontend we can use default metrics for S3 defined by AWS, these metrics can be used as per the use case. AWS CloudFront provides real-time logs for analysis and custom metrics which you can define for alerts using CloudWatch and SNS.
- #### Application Tier
    Fargate integrates very well with AWS CloudWatch to provide the required metrics and logs from the services and tasks. Based on these metrics and logs we can perform various actions such as scaling out or scaling in .We can also use these metrics to create custom alarms that notify the responsible team using SNS or AWS Lambda to perform automated scripts. Incase CloudWatch is not sufficient for the use case we can use third party APM tools. We can have ECR registry in AWS to save all your docker images. 
- #### Database Tier
    RDS provides various metrics such Deadlocks, Connections, Memory/CPU etc. Moreover, for certain instance types you can use performance insights that highlight various SQL queries that consume the respective DB resources and it can showcase the gaps that are causing any DB bottlenecks.
    In case of region failure, we can have a disaster recovery of database using cross-region read replicas or we can transfer snapshot to DR region using AWS Backup. 

- #### Infrastructure as Code (IaC)
    Terraform can be used as IaC to maintain the state of your infrastructure. Terraform is a good tool that have verified providers from different third-party vendors to integrate in your on-premises or public cloud. HCL language used by Terraform makes it easy and simple to implement infrastructure with very little learning curve. Maintaining the state file of Terraform is the very important. This can be managed by saving the state file in S3. 
    In order to provide, consistency and collaboration between different team member we would need to have a repository to maintain the code. Git based solutions can be used for maintaining the code in a repository. Along with Git repository and Git Actions(Workflows) we can create a CI/CD pipeline for deploying IaC and maintaining Terraform state file inside S3 with approval system.

- #### CI/CD
    For frontend and application tier, we can implement CI/CD using AWS Code Services like Code Pipeline, Code Deploy and Code Build. This can also be accomplished using third-party tools like Jenkins, GitHub Actions etc. These AWS resources use IAM roles which provide them with temporary credentials. 

- #### Testing
    We can integrate multiple testing tools along with AWS Code Build or build our own test cases using open-source tools like Selenium, JMeter etc. 

## Fargate vs EKS

An Alternate solution for Application tier would be using AWS EKS for hosting specific task or services. As Kubernetes is an opensource project, it gives us the ability to have multiple hybrid cloud environments and allows us to use opensource load balancers such as NIGNIX etc. Furthermore, we can accommodate niche use cases through EKS. Fargate is more expensive for workloads which continually runs round the clock then EKS. In EKS, we can implement multiple opensource technologies for monitoring like Grafana, Loki etc. AWS Fargate comes with limited networking mode which AWS VPC mode. EKS provides you with granular control of container placement to specific nodes.

