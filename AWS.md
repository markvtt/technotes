### Stuff about AWS

# Cheetsheet

```
aws organizations list-accounts

```


# CDK getting started


```
aws configure
cat ~/.aws/config
cat ~/.aws/credentials

cdk --version
aws sts get-caller-identity
aws configure get region

```

# Install

# Sample express app

```
mkdir sample-express-app
cd sample-express-app
yarn init
question name (sample-express-app):
question version (1.0.0):
question description:
question entry point (index.js): server.js
question repository url:
question author: kylegalbraith
question license (MIT):
question private:
```

```
yarn add express
info No lockfile found.
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...
```

### Create server.js
```
const express = require('express')

const PORT = 8080
const HOST = '0.0.0.0'

const api = express()
api.get('/', (req, res) => {
  res.send('Sample Endpoint\n')
})

api.listen(PORT, HOST)
console.log(`Running on http://${HOST}:${PORT}`)
```

### Create Dockerfile
```
FROM node:10

WORKDIR /src/api

COPY package.json ./
COPY yarn.lock ./

RUN yarn install

COPY . .

EXPOSE 8080
CMD ["node", "server.js"]
```

### Build image
```
docker build -t sample-express-app .
docker run -p 8080:8080 -d sample-express-app
curl localhost:8080
```

# Build a new Project

```
mkdir ecs-ec2-cdk
cd ecs-ec2-cdk

cdk init app --language typescript
npm run build
cdk ls

### Create ECR

```
npm install @aws-cdk/aws-ecr
```

```
import cdk = require('@aws-cdk/core')
import ecr = require('@aws-cdk/aws-ecr')

export class InfrastructureStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props)

    // ECR repository
    const repository = new ecr.Repository(this, 'sample-express-app', {
      repositoryName: 'sample-express-app',
    })
  }
}
```

```
cdk diff
Stack EcsEc2CdkStack
Unable to resolve AWS account to use. It must be either configured when you define your CDK Stack, or through the environment

cdk diff -v
Setting "CDK_DEFAULT_REGION" environment variable to us-east-1
Resolving default credentials
Unable to determine the default AWS account: EnvironmentCredentialsProviderFailure: Variable AMAZON_ACCESS_KEY_ID not set.


set CDK_DEFAULT_REGION=ap-southeast-2
set AWS_DEFAULT_REGION=ap-southeast-2
set AWS_ACCESS_KEY_ID=
set AWS_SECRET_ACCESS_KEY=


cdk deploy

export MY_AWS_ACCOUNT_ID=
aws ecr get-login-password \
        --region ap-southeast-2 | docker login \
        --username AWS \
        --password-stdin ${MY_AWS_ACCOUNT_ID}.dkr.ecr.ap-southeast-2.amazonaws.com
Login Succeeded

aws ecr describe-repositories


docker tag sample-express-app 786529158947.dkr.ecr.ap-southeast-2.amazonaws.com/sample-express-app
docker push 786529158947.dkr.ecr.ap-southeast-2.amazonaws.com/sample-express-app
```


### install required packages

```
npm install @aws-cdk/aws-ecs
npm install @aws-cdk/aws-ec2
npm install @aws-cdk/aws-ecs-patterns
```

### add to stack

```
import ecr = require('@aws-cdk/aws-ecr')
import ecs = require('@aws-cdk/aws-ecs')
import ec2 = require('@aws-cdk/aws-ec2')
import ecsPatterns = require('@aws-cdk/aws-ecs-patterns')

    // ECS cluster/resources
    const cluster = new ecs.Cluster(this, 'app-cluster', {
      clusterName: 'app-cluster',
    })

    cluster.addCapacity('app-scaling-group', {
      instanceType: new ec2.InstanceType('t2.micro'),
      desiredCapacity: 1,
    })

    const loadBalancedService = new ecsPatterns.ApplicationLoadBalancedEc2Service(this, 'app-service', {
      cluster,
      memoryLimitMiB: 512,
      cpu: 5,
      desiredCount: 1,
      serviceName: 'sample-express-app',
      taskImageOptions: {
        image: ecs.ContainerImage.fromEcrRepository(repository),
        containerPort: 8080,
      },
      publicLoadBalancer: true,
    })
```

### Deploy

```
cdk deploy --require-approval never
```


# Example project using ECS and EC2

https://blog.kylegalbraith.com/2019/10/24/how-to-run-docker-containers-via-aws-elastic-container-service/


### Things to look at
https://tomgregory.com/deploying-jenkins-into-aws-ecs-using-cdk/
https://pages.awscloud.com/rs/112-TZM-766/images/2019_0911-CON_Slide-Deck.pdf
https://medium.com/tysonworks/deploy-go-applications-to-ecs-using-aws-cdk-1a97d85bb4cb

