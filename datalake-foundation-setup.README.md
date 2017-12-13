# Quickstart DataLake Architecture

 [**Design Document**](https://reancloud.atlassian.net/wiki/spaces/PLAT/pages/154671303/Big+Data+blueprints+-+design+and+implementation) 

 ![datalake-quickstart-architecture](https://user-images.githubusercontent.com/7883617/30954626-8889ed4c-a44e-11e7-83eb-f1e8d340cd38.png)



## 1. **App VPC Setup Environment**

1. [**App VPC Infrastructure Diagram**](#11-app-vpc-infrastructure-diagram)
1. [**Infrastructure deployed by App VPC Blueprint**](#12-infrastructure-deployed-by-app-vpc-setup-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

### 1.1. **App VPC Infrastructure Diagram**

![app-vpc-setup](https://user-images.githubusercontent.com/19923153/30470022-ba120266-9a10-11e7-8afa-cf8f95808bf3.png)

In this deployment model, the VPC with two private subnets is getting created for application deployment.

### 1.2. **Infrastructure deployed by App VPC Setup Environment**

List of resources created by the App VPC Setup Environment:

1. VPC

    * VPC with the specified CIDR in the input variable is created.

1. Subnets

    * Two Private subnets are created in different AZ in the VPC created in previous setup.

1. Route Tables

    * Two route tables are created for each subnet and associated with the respective subnets.

1. VPC endpoint

    * VPC endpoint is created

1. Log Group

    * A log group is created, in which all flow log of the VPC are stored.

1. Network ACL

### 1.3. **Dependencies**

The base-iam-setup environment has to be deployed in the required Region as the parent environment before deploying the App VPC Setup environment.

### 1.4. **Input variables**

| **Variables**        | **Description**                                                   |
|----------------------|-------------------------------------------------------------------|
| vpc_cidr_block       | CIDR used to create                                               |
| owner                | User ID of the owner                                              |
| environment          | Dev                                                               |
| product_name         | reanplatform                                                      |
| myip                 | IP which should be whitelisted                                    |
| flow_log_traffic_type| Default Value: ALL                                                |
| az_count             | Number of AZ                                                      |
| priv_subnet_names    | Name of the subnet                                                |
| az_cidr_length       | Default Value: 2                                                  |
| az_cidr_newbits      | Default Value: 4                                                  |
| subnet_cidr_length   | Default Value: 1                                                  |
| subnet_cidr_newbits  | Default Value: 8                                                  |
| platform_keypair     | Name of the Keypair to be used for launching the instances        |

### 1.5. **Output Variables**

| **Variables**           | **Description**                            |
|-------------------------|--------------------------------------------|
| VPCId                   | ID for the create VPC                      |
| Subnet1ID               | Subnet ID of created Subnet                |
| Subnet2ID               | Subnet ID of created Subnet                |
| Region                  | Currect Region Name                |

### 1.6. **Configuration steps to be taken before deploying the environment**

1. Add the value to all the keys in Input resource.
1. Add the parent environment name to the depends on resource named **IAM**

## 2. **Bucket Setup Environment**

1. [**Bucket Setup Infrastructure Diagram**](#21-bucket-setup-infrastructure-diagram)
1. [**Infrastructure deployed by Bucket Setup Environment**](#22-infrastructure-deployed-by-bucket-setup-environment)
1. [**Dependencies**](#23-dependencies)
1. [**Input variables**](#24-input-variables)
1. [**Output Variables**](#25-output-variables)

### 2.1. **Bucket Setup Infrastructure Diagram**

![s3 bucket setup](https://user-images.githubusercontent.com/19923153/30578103-c62f1c06-9d2f-11e7-8623-f3e007b60213.png)

In this deployment model, 5 buckets and 3 SNS topic are created.

### 2.2. **Infrastructure deployed by Bucket Setup Environment**

List of resources created by the Bucket Setup Environment:

1. S3 Buckets with forceful destroy enabled

    * SubmissionsBucket
    * CuratedDatasets
    * PublishedData
    * RegionalLambdaBucket
    * AthenaQueryResultsBucket

1. SNS Topics

    * SubmissionsTopic
    * CuratedTopic
    * PublishedTopic

1. Bucket Event Notification to SNS Topic
    On event of adding object to S3 buckets(SubmissionBucket, CuratedDatasets and PublishedData) the notification is set to respective topics.

    * SubmissionBucket's notification is sent to SubmissionsTopic
    * CuratedDatasets's notification is sent to CuratedTopic
    * PublishedData's notification is sent to PublishedTopic

### 2.3. **Dependencies**

No Dependency

### 2.4. **Input variables**

No Input

### 2.5. **Output Variables**

| **Variables**               | **Description**                  |
|-----------------------------|----------------------------------|
| SubmissionBucketARN         | ARN of SubmissionBucket          |
| SubmissionBucketName        | Name of SubmissionBucket         |
| CuratedBucketARN            | ARN of CuratedBucket             |
| CuratedBucketDatasetName    | Name of CuratedBucket            |
| PublishedBucketARN          | ARN of PublishedBucket           |
| PublishedBucketName         | Name of PublishedBucket          |
| RegionalLambdaBucketARN     | ARN of RegionalLambdaBucket      |
| RegionalLambdaBucketName    | Name of RegionalLambdaBucket     |
| AthenaQueryResultsBucketARN | ARN of AthenaQueryResultsBucket  |
| AthenaQueryResultsBucketName| Name of AthenaQueryResultsBucket |
| SubmissionTopicARN          | ARN of SubmissionTopic           |
| CuratedTopicARN             | ARN of CuratedTopic              |
| PublishedTopicARN           | ARN of PublishedTopic            |


## 3. **IAM Setup Blueprint**

1. [**IAM Setup Infrastructure Diagram**](#31-iam-setup-infrastructure-diagram)
1. [**Infrastructure deployed by IAM Setup Blueprint**](#32-infrastructure-deployed-by-iam-setup-blueprint)
1. [**Dependencies**](#33-dependencies)
1. [**Input variables**](#34-input-variables)
1. [**Output Variables**](#35-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#36-configuration-steps-to-be-taken-before-deploying-the-environment)

### 3.1. **IAM Setup Infrastructure Diagram**

![iam-setup](https://user-images.githubusercontent.com/19923153/31003576-0f9f87be-a50e-11e7-8eff-b48402b5bbf6.png)

In this deployment model all required roles and there corresponding policies are created

### 3.2. **Infrastructure deployed by IAM Setup Blueprint**

List of resources created by the IAM Setup Blueprint:

1. IAM Roles

    * AnalyticsAccess
    * CopyDataRole
    * ElasticsearchAccess
    * KinesisStreamBucketRole
    * LambdaRole
    * RegisterKibanaDashboardRole
    * CuratedBucketAccess
    * QSRedshiftRole
    * SubmissionsBucketAccess
    * AWSGlueServiceRoleDefault

### 3.3. **Dependencies**

The app-vpc-setup.blueprint.reandeploy Blueprint and iam-setup.blueprint.reandeploy Blueprint has to be deployed in the required Region as the parent environment before deploying the IAM Setup Blueprint.

### 3.4. **Input variables**

| **Variables**        | **Description                                 |
|----------------------|-----------------------------------------------|
| DatasetS3BucketName  | Dataset bucket name                           |
| QSS3BucketName       | Bucket name where lambda code will be present |
| GlueStackPrefix      | Prefix for aws-glue stack                     |

### 3.5. **Output Variables**

| **Variables**                  | **Description**                    |
|--------------------------------|------------------------------------|
| AnalyticsAccessARN             | ARN of AnalyticsAccess role        |
| CopyDataRoleARN                | ARN of CopyDataRole                |
| ElasticSearchLambdaIAMPolicyARN| ARN of ElasticSearchLambdaIAMPolicy|
| ElasticsearchAccessARN         | ARN of ElasticsearchAccess         |
| KinesisStreamBucketRoleARN     | ARN of KinesisStreamBucketRole     |
| LambdaRoleARN                  | ARN of LambdaRole                  |
| RegisterKibanaDashboardRoleARN | ARN of RegisterKibanaDashboardRole |
| CuratedBucketAccessARN         | ARN of CuratedBucketAccess         |
| QSRedshiftRoleARN              | ARN of QSRedshiftRole              |
| SubmissionsBucketAccessARN     | ARN of SubmissionsBucketAccess     |
| AWSGlueServiceRoleDefaultARN   | ARN of AWSGlueServiceRoleDefault   |

### 3.6. **Configuration steps to be taken before deploying the environment**

1. Add the value to all the keys in Input resource.
1. Add the parent environment name to the depends on resources.

## 4. **Elasticsearch Environment**

1. [**Elasticsearch Environment Infrastructure Diagram**](#41-elasticsearch-environment-infrastructure-diagram)
1. [**Infrastructure deployed by Elasticsearch Environment**](#42-infrastructure-deployed-by-elasticsearch-environment)
1. [**Dependencies**](#43-dependencies)
1. [**Input variables**](#44-input-variables)
1. [**Output Variables**](#45-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#46-configuration-steps-to-be-taken-before-deploying-the-environment)

### 4.1. **Elasticsearch Environment Infrastructure Diagram**

![elasticsearch-setup](https://user-images.githubusercontent.com/9102229/31379798-fdcfad3a-adcc-11e7-9641-e609336121f0.png)


### 4.2. **Infrastructure deployed by Elasticsearch Environment**

List of resources created by the Elasticsearch Environment:

1. Elasticsearch domain

    * Create the elasticsearch domain for indexing & storing event metadata.

1. Lambda function

    * Creates the AWS lambda function for handling bucket events.
    
1. SNS Topic subscriptions

    * Configure AWS lambda as subscriber to SNS topics for each of the following S3 bucket events:
         - Submissions bucket
         - Curated data bucket
         - Published data bucket
 
1. Lambda permissions

    * Creates the AWS lambda permissions for invoking the lambda via the individual SNS subscriptions mentioned above.



### 4.3. **Dependencies**

The bucket-setup and iam-setup environment has to be deployed
in the required Region as the parent environment before deploying the Elasticsearch Environment.

**Dependent varibles from parent environments**

| **Variables**             | **Description**                                                                |
|---------------------------|--------------------------------------------------------------------------------|
| LambdaRoleARN             | IAM role ARN created by iam-setup environment for execution of Lambda function |
| RegionalLambdaBucketName  | Regional Lambda bucket ARN from bucket-setup environment for Lambda code       |
| SubmissionTopicARN        | Submissions SNS topic ARN from bucket-setup environment for subscriptions      |
| CuratedTopicARN           | Curated data SNS topic ARN from bucket-setup environment for subscriptions     |
| PublishedTopicARN         | Published data SNS topic ARN from bucket-setup environment for subscriptions   |


### 4.4. **Input variables**

| **Variables**                   | **Description**                                                           |
|---------------------------------|---------------------------------------------------------------------------|
| NodeCount                       | Elasticsearch cluster configuration node count                            |
| NodeType                        | Elasticsearch cluster configuration node type                             |
| DomainName                      | Name of the elasticsearch domain                                          |
| ElasticsearchAccessIP           | IP address for restricted access to Elasticsearch                         |
| ObjectCreatedLambdaQSS3ObjectKey| S3 object key referring to the lambda function code as in QS S3 bucket for copying it to regional bucket   |
| LambdaArtifactName              | Name of AWS lambda artifact to be used from the regional Lambda bucket    |
| DashboardRegistryArtifactName   | Name of the Artifact used for registering the Kibana dashboard            |

### 4.5. **Output Variables**

| **Variables**           | **Description**                                    |
|-------------------------|----------------------------------------------------|
| KibanaURL               | Kibana URL to access Kibana dashboard              |
| ElasticsearchEndpoint   | Elasticsearch endpoint for access                  |
| ElasticsearchDomainARN  | Elasticsearch resource ARN                         |

### 4.6. **Configuration steps to be taken before deploying the environment**

1. Add the value to all the keys in Input resource.
1. Add the parent environment name to the depends on resources.

## 5. **Redshift Cluster Environment**

1. [**Redshift Cluster Environment Infrastructure Diagram**](#51-redshift-cluster-environment-infrastructure-diagram)
1. [**Infrastructure deployed by Redshift Cluster Environment**](#52-infrastructure-deployed-by-redshift-cluster-environment)
1. [**Dependencies**](#53-dependencies)
1. [**Input variables**](#54-input-variables)
1. [**Output Variables**](#55-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#56-configuration-steps-to-be-taken-before-deploying-the-environment)

### 5.1. **Redshift Cluster Environment Infrastructure Diagram**

![redshift cluster](https://user-images.githubusercontent.com/7883617/31219070-cdf41d2c-a9d9-11e7-83de-cb996c92ba26.png)

### 5.2. **Infrastructure deployed by Redshift Cluster Environment**

List of resources created by the Redshift Cluster Environment:

1. Security Group

    * Create the Redshift security group which enables JDBC port.

1. Redshift Subnet Group

    * Creates the Redshift cluster subnet group. The group consists of subnets identified by Subnet1ID and Subnet2ID from the parent environment app-vpc-setup

1. Redshift Cluster

    * Creates the Redshift cluster.


### 5.3. **Dependencies**

The app-vpc-setup and iam-setup environment has to be deployed
in the required Region as the parent environment before deploying the Redshift Cluster Blueprint.

**Dependent varibles from parent environments**

| **Variables**        | **Description**                                                   |
|----------------------|-------------------------------------------------------------------|
| VPCId                | App-VPC ID where the redshift cluster will get deployed           |
| VPC-CIDR             | App-VPC CIDR where the redshift cluster will get deployed         |
| Subnet1ID            | App-VPC private subnet1 id                                        |
| Subnet2ID            | App-VPC private subnet2 id                                        |
| QSRedshiftRoleARN    | QSRedshiftRole IAM role ARN                                       |


### 5.4. **Input variables**

| **Variables**                   | **Description**                                         |
|---------------------------------|---------------------------------------------------------|
| owner                           | User ID of the owner                                    |
| environment                     | Dev                                                     |
| product_name                    | reanplatform                                            |
| DatabaseName                    | The name of the first database to be created when the cluster is created. Default is 'quickstart'                                                 |
| DatabasePort                    |  The port number on which the cluster accepts incoming connections. Default is '5439'                                                              |
| NodeType                        | The node type to be provisioned for the cluster. Default is 'dc1.large'         |
| NumberOfNodes                   | The no of compute nodes in the cluster. Default value 1 |  
| RedshiftPassword                | Password for the master DB user. Default is 'Password1' |
| RedshiftUsername                | Username for the master DB user. Default is 'datalake'  |
| RedshiftSkipFinalSnapshot       | Determines whether a final snapshot of the cluster is created before Amazon Redshift deletes the cluster. If true , a final cluster snapshot is not created. If false , a final cluster snapshot is created before the cluster is deleted. Default is false.                                                                                   |
| RedshiftFinalSnapshotIdentifier | The identifier of the final snapshot that is to be created immediately before deleting the cluster. If this parameter is provided, RedshiftSkipFinalSnapshot must be false.                                                    |

### 5.5. **Output Variables**

| **Variables**           | **Description**                                    |
|-------------------------|----------------------------------------------------|
| RedshiftConnectionURL   | Connection URL to connect to redshift cluster      |

### 5.6. **Configuration steps to be taken before deploying the environment**

1. Add the value to all the keys in Input resource.
1. Add the parent environment name to the depends on resources.
