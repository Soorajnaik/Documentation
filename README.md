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
- Terraform
- Github
- Docker
- Jenkins
- AWS                                                                                
- Prometheus
- Grafana
- Kubernetes (AWS EKS)
- EFK
- ELK

### Terraform
Terraform is an open-source infrastructure as code software tool that enables you to safely and predictably create, change, and improve infrastructure. It is a declarative language, so your goal is to describe the infrastructure you want, and Terraform will figure out how to create it. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle.
> We use this tool to build the infrastucture for this project such as VPC, Subnets, Kubernetes cluster with one nodegroup with min 1 spot machine, max 5. We save the terrafrom state file in the S3 bucket so that even if we accidentally delete this file locally it would be present in S3.

Terraform can manage infrastructure on multiple cloud platforms. The human-readable configuration language helps you write infrastructure code quickly. Terraform's state allows you to track resource changes throughout your deployments. Terraform stores state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures. The state file is commonly stored either on a local machine, in a remote storage location (like a storage account in Azure, or S3 bucket in AWS), or in Terraform cloud. By default, it is stored on the local machine and is named “terraform. tfstate”. Some of the code we use in terraform are -

- terraform validate (To validate terraform code)
- terraform init (Initialize your Terraform working directory)
- terraform plan (To plan the infrastructure)
- terraform apply (To create the infrastructure)
- terraform destroy (To cleanup the whole infrastructure)

### Github
GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere. It is an online software development platform used for storing, tracking, and collaborating on software projects. It enables developers to upload their own code files and to collaborate with fellow developers on open-source projects.
> Created an organisation in github and added users to that organisation. 
> Will create two github repository Devops and Production and will assign Branch protection rules to the `main` branch of both the repositories so that the repository should not accept direct commits to main branch it should accept only merges to the main branch. 
> For every change any user make they need to send a PR and other users should approve to merge into the `main` branch. 
> By this we can avoid any unnecessary merges to the main branch

### Docker
Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allows you to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host. You can easily share containers while you work, and be sure that everyone you share with gets the same container that works in the same way.

Avantages of using docker is
- Fast, consistent delivery of your applications
- Responsive deployment and scaling
- Running more workloads on the same hardware

