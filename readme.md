# Servian DevOps Tech Challenge - Tech Challenge App

[![Build Status][circleci-badge]][circleci]
[![Release][release-badge]][release]
[![GoReportCard][report-badge]][report]
[![License][license-badge]][license]

[circleci-badge]: https://circleci.com/gh/servian/TechChallengeApp.svg?style=shield&circle-token=8dfd03c6c2a5dc5555e2f1a84c36e33bc58ad0aa
[circleci]: https://circleci.com/gh/servian/TechChallengeApp
[release-badge]: http://img.shields.io/github/release/servian/TechChallengeApp/all.svg?style=flat
[release]:https://github.com/Servian/TechChallengeApp/releases
[report-badge]: https://goreportcard.com/badge/github.com/Servian/TechChallengeApp
[report]: https://goreportcard.com/report/github.com/Servian/TechChallengeApp
[license-badge]: https://img.shields.io/github/license/Servian/TechChallengeApp.svg?style=flat
[license]: https://github.com/Servian/TechChallengeApp/license

## Overview

This is the Servian DevOps Tech challenge. It uses a simple application to help measure a candidate's technical capability and fit with Servian. The application itself is a simple GTD Golang application that is backed by a Postgres database.

Servian provides the Tech Challenge to potential candidates, which focuses on deploying this application into a cloud environment of choice.

More details about the application can be found in the [document folder](doc/readme.md)

## Taking the challenge

For more information about taking the challenge and joining Servians's amazing team, please head over to our [recruitment page](https://www.servian.com/careers/) and apply there. Our recruitment team will reach out to you about the details of the test and be able to answer any questions you have about Servian or the test itself.

Information about the assessment is available in the [assessment.md file](ASSESSMENT.md)

## Found an issue?

If you've found an issue with the application, the documentation, or anything else, we are happy to take contributions. Please raise an issue in the [github repository](https://github.com/Servian/TechChallengeApp/issues) and read through the contribution rules found the [CONTRIBUTING.md](CONTRIBUTING.md) file for the details.

## Deployement Steps - Tech Challenge 

1. Run the Cloudformation command to create EC2 instance along with Postgress database

 aws cloudformation create-stack --stack-name go-app-server-2 --template-body file://template.yaml --parameters ParameterKey=InstanceType,ParameterValue=t2.micro ParameterKey=KeyName,ParameterValue=tech-aws ParameterKey=SubnetId1,ParameterValue=subnet-97243fde ParameterKey=VpcId,ParameterValue=vpc-2bb1b74c ParameterKey=RdsUsername,ParameterValue=goAdmin_User ParameterKey=RdsPass,ParameterValue=goAdminPassword --tags Key=Name,Value="WebServer"

 2. Go to AWS > Cloudformation > Stacks to see the stack being generated and track progress. This would have generated an Ec2 instance with go and docker installed and an RDS Psotgress database

 3. To deploy the application on the EC2 instance, we need to SSH into the EC2 instance and deploy the app using Docker. But before that, we need to update the config.toml file with the details of the new RDS database created from the previous step. 

Example - 

"DbUser" = "goAdmin_User" (The username passed while creating the stack)
"DbPassword" = "goAdminPassword" (The password passed while creating the stack)
"DbName" = "serviandb" (The name of the database, curently hardcoded in template.yaml file )
"DbPort" = "5432"  (By default database port in AWS RDS)
"DbHost" = "br16qqrjxubgz32.cp4ciiixghw5.ap-southeast-2.rds.amazonaws.com" (The database endpoint from RDS instance)
"ListenPort" = "3000"

4. Create a new docker image with the new configuration file and push it to docker hub. 

docker build . -t servian/techchallengeapp:latest
docker push

5. Run a container on the EC2 instance by the command

docker run -d -p 3000:3000 servian/techchallengeapp:latest

6. Open the Public IP address of the EC2 instance on 3000 port and the web app with the connected database should be visible. 


