# _Nodejs Project_
### Project Summary
> The main aim of the project is to automate the deployment of the given nodejs 
> project so that it can be deployed into various enviornment without any issue
> just by editing the variables according to the requirement of that enviornment.  
> Then to monitor the software in that enviornment for any issues using various 
> tools. Finally will get the logs from the cluster in which the application has 
> been deployed to the EFK setup 

### Project Justification
This project was needed for automation of the deployment process in any enviornment hence by bringing up the enviornment to deploying the application, everything will be automated.

### Tools used in this Projects
- AWS
- Terraform
- Github
- Docker
- Jenkins
- Kubernetes (AWS EKS)
- Monitoring tools (Prometheus, Grafana)
- Logging tools (ELK, EFK)

### AWS
AWS (Amazon Web Services) is a secured cloud services platform that offers compute power, database storage, content delivery and various other functionalities.

### Terraform
Terraform is an open-source infrastructure as code software tool that enables you to safely and predictably create, change, and improve infrastructure. 

### Github
GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere. 

### Docker
Docker is a software platform that simplifies the process of building, running, managing and distributing applications.

### Jenkins
Jenkins is an open source continuous integration/continuous delivery and deployment (CI/CD) automation software DevOps tool written in the Java programming language.

### Kubernetes (EKS)
Amazon Elastic Container Service for Kubernetes (EKS) is a cloud-based container management service that natively integrates with Kubernetes to deploy applications.

### Monitoring Tools
Prometheus is a monitoring solution for storing time series data like metrics. Grafana allows to visualize the data stored in Prometheus (and other sources).

### Logging Tools 
EFK stands for Elasticsearch, Fluentd, and Kibana. EFK is a popular and the best open-source choice for the Kubernetes log aggregation and analysis. Elasticsearch is a distributed and scalable search engine commonly used to sift through large volumes of log data.

Elasticsearch, Logstash, and Kibana when used together, form an end-to-end stack (ELK Stack) and real-time data analytics tool that provides actionable insights from almost any type of structured and unstructured data source.

## Project Explanation

#### Phase 1 
We created an organistation in github and added users to that organisation. Created two github repository Devops and Production and assigned Branch protection rules to the `main` branch of both the repositories so that the repository should not accept direct commits to main branch it should accept only merges to the main branch, For every change any user make they need to send a PR and other users should approve to merge into the `main` branch. By this we can avoid any unnecessary merges to the `main` branch. In Devops repository we will push helm chart, Jenkinsfile and Terraform files and in Production repository we will push Dockerfile and the Code.
The code we pushed to Production repository is 
> https://github.com/VV-MANOJ/AatmaaniProject

To create branch protection rules we can refer this website.
> https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/managing-a-branch-protection-rule

First we created an EC2 instance and installed Terraform in it, steps for installation can be refered here
> https://learn.hashicorp.com/tutorials/terraform/install-cli

After installation we are not using provider here we will log into our ec2 instance and type aws configure and configure the credentials and after that we will press Terraform init and initialize it, we do this for security purpose so that our AWS credentials are not exposed. After initialization we write the terraform configuration file to create a VPC, Private and Public Subnets, nat-gateway, IGW, Routetables, adding routes to route tables, S3, New EC2 named Jumpbox, ECR to store images, 3 variable files for 3 namespaces and push this configuration file to github.
We push this code to Github Devops repository. We save the terrafrom state file in the S3 bucket so that even if we accidentally delete this file locally it would be present in S3. For ceration of configuration file for aws resources we can refer following website
> https://registry.terraform.io/providers/hashicorp/aws/latest/docs

#### Phase 2

In this phase we login to the Jumpbox created using terraform in our custom VPC and install java and jenkins give the username and password and login to the jenkins dashboard. We can install Java, jenkins and setup jenkins refering to this link
> https://www.jenkins.io/doc/book/installing/linux/

After jenkins installation we will install aws cli in jumpbox for installation we can refer this website

> https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

After that we will install docker in the Jumpbox installation steps are as per document
> https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04

We then connect the Jumpbox to the EKS cluster for this we can follow the following document
> https://aws.amazon.com/premiumsupport/knowledge-center/eks-cluster-connection/

For autoscaling of nodes in the cluster we deploy cluster autoscaler as a deployment in the kubernetes cluster to install cluster autoscaler we can refer the following document
> https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html

After this we will deploy the metric server in to the kubernetes cluster. These metrics will drive the scaling behavior of the deployments. To install the metrics server we can refer the following website

> https://www.eksworkshop.com/beginner/080_scaling/deploy_hpa/

Then we will create three namespaces for different enviornment
- dev
- qa
- prod

After this we will go into jenkins user by typing `sudo -i -u jenkins`
and type AWS configure there and will give all the credential details and exit the user. And will test if Jumpbox is connected to EKS by executing kubectl commands.

#### Phase 3

Now we created the dockerfile for the given code after creation of the dockerfile manually we build  image and run the image in a docker container to see if it is working properly. When the image works properly we push that dockerfile to github Production repository. So now in production repository application code and dockerfile will be present.
Now will create Helm chart directory in the name of nodejs-project and create three different value files for 3 different enviornment dev,qa,prod such that DEV value files should create 1 pod in the dev enviornment, QA value file should create 1 pod in qa enviornment and PROD value file should create 2 pods in prod enviornmnet. Then push this helm folder and value files to Devops repository. For Installing helm and using helm we can refer following  documents
> https://helm.sh/docs/intro/install/
> https://helm.sh/docs/intro/using_helm/

#### Phase 4

Now will create a jenkinsfile for all the enviornment

For `dev` enviornment will cerate a shell script named build_push.sh and the task of that script is go to production folder in the cloned repository do dockerbuild, tag that image with commit-id and dev-latest and push both the tagged image to AWS ECR. Should take the commit-id of dev-latest and use that tag in helm to install and create a pod using that image. Will push this script to Devops repository. Now will cerate a jenkinsfile named jenkinsfile-dev and in that we will create 4 stages.
> Git Devops repo -- https://github.com/Aatmaani-org/Devops.git
> Git Production repo -- https://github.com/Aatmaani-org/Production.git
> Shell script -- Sooraj/build_push.sh (In Devops Repo)
> Jenkinsfile path -- Sooraj/Jenkinsfile-dev (In Devops Repo)

- stage-1 -- will clean all the directories previously cloned

- stage-2 -- Will create a folder named `warnerbroproduction` go into that folder and clone the production repository

- stage-3 -- Will create a folder named `warnerbrodevops` go into that folder and clone the devops repository

- stage-4 -- Will go to a folder containing shell script created and pushed above and will run that shell script which will tag,push image and deploy pod  to dev enviornment

- Post -- On failure it should send a slack message to our slack channel saying that the job has failed

For `qa` enviornment there would be one stage in stages and one post.Will create an shell script for this stage the task of this script is to pull the image using the commit-id tag that image with qa-latest tag, push the image to the ecr and after that deploy the pod using helm with image tag of commit-id in qa namespace using values-qa.yaml values file the shell file name is qa_tag.sh

- stage-1 -- will go into this path `warnerdevops/Devops/Sooraj/` and run the shell script named qa_tag.sh 

- post -- If job fails it will send an alert to slack that the job has failed

For `prod` enviornment we would create a shell script the task of that shell script is to pull the image with commit-id having qa-latest tag, tag that image with prod-latest tag and push that image to ECR and now will deploy this image to to production enviornment using commit-id and values file of values-prod.yaml here in helm command we will use --atomic flag so that if the pod fails to run then pod can rollback its image to the previous stable version, In this deployment we will enable horizontal pod autoscaler to autoscale the pod if there is a load in production enviornment. Pushed this shellscript to git devops repository.

- stage-1 -- will go into this path `warnerdevops/Devops/Sooraj/` and run the shell script cerated with the name prod_tag.sh
 
- post-1 -- If the job fails slack will send the notification to our channel

#### Phase 5

After all the jenkinsfile creation and deployment of the application in various enviornment we need some tool to monitor the pods and if the pod stops for any reason we want an alert to our slack for that we install tools such as prometheus,alertmanger grafana in our EKS cluster and node exporter.
First we will create a namespace called prometheus, then will install `prometheus` using helm after that we are going to install `grafana` for that we will create a namespace called grafana and install grafana in that namespace using helm.Then will log into grafana and import a dashboard select prometheus as the end point under prometheus data sources and click import. For cluster monitoring dashboard we can use 3119 dashboard id and for pods monitoring we can use 6417 dashboard id. Then it will show all the data in the dashboard. For above installation we can refer the below web-page

> https://www.eksworkshop.com/intermediate/240_monitoring/deploy-prometheus/

We will setup prometheus and grafana for monitoring Loadbalancer from aws and  services from the Kubernetes cluster. If the ALB target are Unhealthy and if the pod is not in running state we should get an alert regarding the state through the alertmanager so how to configure the alertmanager. For configuring alertmanager and setting up the alert we can refer the following website

> https://medium.com/devops-dudes/prometheus-alerting-with-alertmanager-e1bbba8e6a8e

For setting various type of alerts we can refer the below link

> https://awesome-prometheus-alerts.grep.to/rules.html

![alt text](https://github.com/Soorajnaik/Documentation/blob/d89103866fd7b6f25d334562a0171eb0b9637ee4/Untitled%20Diagram.drawio.png?raw=)

#### Phase 6 

After monitoring we want logging tool to be installed in an EKS cluster such as EFK or ELK so that we can get logs in a centralised point for acheiving this we can use these three tools Elasticsearch, Fluentd/Fluentbit, and Kibana (EFK) stack. We’ll set up Fluentd/Fluentbit as a DaemonSet so it runs on every Kubernetes worker node and fluentd/Fluentbit will collect all the logs and deliver it to the Elasticsearch cluster, where it will be indexed and stored and Kibana is a data visualization frontend which allows us to explore your Elasticsearch log data through a web interface, and build dashboards and queries to quickly answer questions and gain insight into our application.
For installation we can refer this documentation

> https://www.digitalocean.com/community/tutorials/how-to-set-up-an-elasticsearch-fluentd-and-kibana-efk-logging-stack-on-kubernetes



![alt text](https://github.com/Soorajnaik/Documentation/blob/47107c3fc20022da42db3f7ab72df868ef914267/EFK.drawio.png?raw=)

