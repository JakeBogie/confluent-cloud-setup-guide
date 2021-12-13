![image](https://github.com/hendrasutanto/examples/blob/7.0.0-post/images/confluent-logo-300-2.png)

# Overview

This Confluent Cloud setup guide will help you to setup a base cluster in your Confluent Cloud account.

![image](https://github.com/hendrasutanto/examples/blob/7.0.0-post/cp-quickstart/images/quickstart.png)

# Prerequisites

* User account in [Confluent Cloud](https://www.confluent.io/confluent-cloud/tryfree/).
* Local install of the [Confluent CLI](https://docs.confluent.io/confluent-cli/current/install.html) v2.2.0 or later.
* Local install of the [Docker Desktop](https://docs.docker.com/get-docker/).
* Local install of the [jq](https://github.com/stedolan/jq/wiki/Installation) tool.
* AWS account - You'll be creating a fully-managed sink connector to an object storage.
  - Setup AWS [Access Keys](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)
  - Bucket Name - Create the object storage and have the name of the bucket ready.
  - Region - Note which region you are deploying your object storage resource in.
  - [IAM policy](https://docs.confluent.io/cloud/current/connectors/cc-s3-sink.html#cc-s3-bucket-policy) configured for bucket access.
* Local install of the [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

# Setup

* Clone the examples GitHub repository and check out the 7.0.0-post branch.
```bash
git clone https://github.com/confluentinc/examples.git
cd examples
git checkout 7.0.0-post
```

* Change directory to the cp-quickstart:
```bash
cd cp-quickstart
```

* Log in to Confluent Cloud with the command confluent login, and use your Confluent Cloud username and password. The --save argument saves your Confluent Cloud user login credentials or refresh token (in the case of SSO) to the local netrc file.
```bash
confluent login --save
```

# Step 1 - Setup base cluster

## Confluent Cloud

* Choose the same region where your object storage resource is deployed.
* This quickstart for Confluent Cloud leverages 100% Confluent Cloud services, including a [ksqlDB application](statements-cloud.sql) which builds streams and tables using Avro, Protobuf and JSON based formats. After logging into the Confluent CLI, run the command below and open your browser navigating to https://confluent.cloud. Note: the demo creates real cloud resources and incurs charges.

```bash
./start-cloud.sh
```

### Advanced usage

You may explicitly set the cloud provider and region. For example:

```bash
CLUSTER_CLOUD=aws CLUSTER_REGION=us-west-2 ./start-cloud.sh
```

Here are the variables and their default values:

| Variable | Default |
| --- | --- |
| CLUSTER_CLOUD | aws |
| CLUSTER_REGION | us-west-2 |

### Cleaning Up

To destroy this demo and its Confluent Cloud resources, call the bash script stop-cloud.sh and pass in the client propoerties file auto-generated in the step above. By Default, this deletes all resources, including the Confluent Cloud environment specified by the service account ID in the configuration file.

```bash
./stop-cloud.sh stack-configs/java-service-account-<SERVICE_ACCOUNT_ID>.config
```

Any Confluent Cloud example uses real Confluent Cloud resources. After you are done running a Confluent Cloud example, manually verify that all Confluent Cloud resources are destroyed to avoid unexpected charges.

# Step 2 - Setup S3 Sink connector

* Within Confluent Cloud, click on Connectors. You should see a list of connectors under Fully Managed.
* Click on Connect for the Amazon S3 Sink.
* Complete the configuration details.

    | Configuration Setting               | Value                         |
    |------------------------|-----------------------------------------|
    | Which topics do you want to get data from? | `ACCOMPLISHED_FEMALE_READERS`                      |
    | API Key                              | Generate a new API Key with Global access |
    | API Secret                           | Generate a new API Key with Global access |
    | Amazon Access Key ID                       | Enter your Amazon Access Key ID                    |
    | Amazon Secret Anccess Key                  | Enter your Amazon Secret Access Key                |
    | Bucket Name                                | Enter the name of your bucket/container            |
    | Input Kafka record value format            | JSON_SR                                            |
    | Output Message Format                      | AVRO                                               |
    | Time Interval                              | HOURLY                                             |
    | Flush Size                                 | 1000                                               |
    | Max span of record time before scheduled rotation          | 600000                                               |
    | Max span of record time before rotation                    | 600000                                               |
    | Tasks                                      | 1                                                  |
    | Name                                       | Enter any connector name                           |

    Your Amazon Access Key ID and Amazon Secret Access Key can be found in your AWS account under your security credentials
* View the connector, its status, and metrics on the Connectors page.
* Now let’s check on your S3 bucket. It could take up to 10 minutes before you can see some records in your S3 bucket.
