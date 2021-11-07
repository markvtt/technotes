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

# Build a new Project

```
mkdir ecs-ec2-cdk
cd ecs-ec2-cdk

cdk init app --language typescript
npm run build
cdk ls


```




# Example project using ECS and EC2

https://blog.kylegalbraith.com/2019/10/24/how-to-run-docker-containers-via-aws-elastic-container-service/

