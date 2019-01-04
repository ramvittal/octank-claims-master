# Octank Claims Master

This is a prototype for migrating on-prem 3-tier claims web app, oracle DB and batch claims processing to AWS using Serverless framework 
and services such as AWS SAM, AWS Lambda, AWS Step Functions, Amazon Aurora, Amazon CloudWatch, AWS Amplify, Amazon Cognito, Amazon API Gateway,
Angular, S3, CloudFront, CodePipeline etc.
This repo provides information on how to setup the environment for migration to AWS and code examples for building the solution. 

## AWS Serverless Claims Architecture
![Image of AWS Serverless Claims Architecture]
(https://github.com/ramvittal/octank-claims-master/blob/master/aws-serverless-claims-final.png)

## Setup Instructions

### Setup baseline AWS resources 

1. Run oracle2aurorastack.json CFN template to create AWS resources
2. Example inputs for CFN template:  
     VPC cidr block: 20.0.0.0/16  
     Subnet 1 : 20.0.0.0/24  
     Subnet 2 : 20.0.1.0/24  
     MyIp: 105.251.233.0/24  
     Aurora creds: auradmin/auradmin123

### Setup and Launch Oracle DB
1. Connect to ec2 oracle linux instance using ec2-user
2. Change to root user: su root
3. Change directory: cd /u01/script
4. In oracleipchg.sh Replace IP address with your ec2 oracle linux private IP and run it as ./oracleipchg.sh
5. Switch to oracle user - su oracle.  And restart oracle ./oraclerestart.sh
6. Create oracle sequence tables CLAIM_SEQ, PATIENT_SEQ in oracle CLAIMS db


### Setup and Launch DMS

1.	Create Oracle source endpoint   
        servername=<$ec2 private ip>  
        Port=1521  
        SID=orcl  
        username=claims  
        password=claims123  
        useLogminerReader=N on Advanced extra connection attributes
2.	Create rds aurora target endpoint. Use all defaults
3.	Create task using source and target endpoints. 
        Migration type = migrate existing and ongoing changes  
        Schema = CLAIMS

### Deploy BackSync Lambda & API Services for Angular 

  Follow instructions from this repo - https://github.com/ramvittal/octank-claims-backsync-api  

### Deploy Lambda for Claims Processor

  Follow instructions from this repo - https://github.com/ramvittal/octank-claims-processor  

### Deploy Angular Claims Website
  Follow instructions from this repo - https://github.com/ramvittal/octank-claims-web  

### Setup Batch Claims Processor

1.	Setup a step function with two states as follows:
  a.	Process Claims :  Point this to Claims Processor lambda arn
  b.	Update Claims: Point this to Claims Backsync lambda arn
  c.	Associate a step function role giving access to invoke the above lambdas
2.	Setup a CloudWatch Event Timer Rule to invoke above step function
  a.	Configure a fixed rate schedule and choose target as step function
  b.	Configure input with following: { "requestId": "octank-claims-batch", "claimStatus": "Submitted" }

### Setup QuickSight for reporting

1. Add a new security group to Aurora DB, to give access to Quicksight - see details here https://docs.aws.amazon.com/quicksight/latest/user/enabling-access-rds.html
2. Add a new Aurora RDS data source to QuickSight for reporting

### Setup Cloudfront
1. Serve Serverless Claims website via cloudfront - https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-cloudfront-walkthrough.html

### Setup CodePipeline 

1. As an example, octank-claims-processor is configured for codepipeline. You can use the buildspec.yaml as reference to build your pipeline. 
2. You can use this AWS documentation as reference for setup - https://docs.aws.amazon.com/lambda/latest/dg/build-pipeline.html



## Authors

* **Ram Vittal** - *Initial work* - [RamVittal](https://github.com/ramvittal)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments
