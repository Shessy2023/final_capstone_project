# Deploying a microservices-based architecture application

## Name: Oluwasesan O. Oluwayemi

## Altschool Student ID: ALT/SOE/023/2015

## Overview of the capstone project

#### This guide will walk you through the process of deploying a microservices-based application, specifically the Socks Shop, on Kubernetes using Infrastructure as Code (IaaC) for rapid and reliable deployment.

#### A microservices architecture divides an application into small, independent services that communicate over a network. In this project, we'll deploy SockShop, a containerized microservices-based application, using Kubernetes for orchestration.

#### Core DevOps practices will be employed to automate the deployment of SockShop, this DevOps practices will include: - Infrastructure as a Code to automate the provisioning of our resources - Container Orchestration - Monitoring, Logging & Alerts - Continous Development and Continous Integration - Security

#### SockShop includes several microservices, such as the front-end, carts, catalouge, payments and shipping. Each service operates independently but share relevant data accross the deployment. Each microservice is packaged in a docker image and kubernetes is used to deploy and orchestrate the communication between the different services.

## My Architecture Diagram

### Here is a straightforward representation focusing on the key components og my deployment without too much details. I'll include the main elements: user, client/browser, Kubernetes cluster, microservices, Jenkins (or GitHub Actions which was used in this case), Ingress controller, and other essential steps. 
![architectural design](/Snapshots/architechtural_design.png)


## I set up my project folder as shown in the folder 

![folder_tree](/Snapshots/folder_structure_tree.png)


## Prerequisites
#### - A Kubernetes cluster
#### - An IaaS provider account
#### - Ansible or Terraform installed
#### - Prometheus, Grafana, and Alertmanager installed
#### - Access to the Socks Shop Microservices Demo repository

### The follwing processes assumes that we are familiar with some basic of  Kubernetes and that you should have these installed:

#### - AzureCLI
#### - Azure account with active subcription
#### - AzCLI configured on your terminal
#### - Terraform installed
#### - Kubectl
#### - Helm

## Note: 
#### - The IaaS provider used for this deployment is azure and terraform was used for this deployment.

#### - the snapshots from this deployment are available in the snapshots directory.

#### - The app and everything needed for this deployment are available in the sock-shop-app directory and within it is several different types of subdirectories


## Deployment Steps

### 1. Clone the Repository:
#### Start by cloning the Socks Shop repository to my local machine 

#### - `git clone https://github.com/microservices-demo/microservices-demo.git` 

#### - Setup your folder structure. Navigate to the terraform directory and write/confirm your configuration files to provision a Kubernetes cluster on Microsoft Azure. The terraform ![main.tf](/Snapshots/main.png) file includes configurations to create a resource group and an Azure Kubernetes Service within that group. The terraform configuration handles a basic configuration of a Azure Kubernetes Cluster. It creates a resource group and creates a fully managed kibernetes cluster in in the specified azure subscription.

### 2. Create the output.tf file, provider.tf, variables.tf, ssh.tf configuration, as shown below

![output.tf](/Snapshots/output.png)

##### output.tf

![provider.tf](/Snapshots/provider.png)

##### provider.tf

![ssh.tf](/Snapshots/ssh.png)

##### ssh.tf

![variables.tf](/Snapshots/variables.png)

##### variables.tf


### 3. Once these configurations are done, we can run the following terraform commands:
#### Do: 
##### - `terraform init`
##### - `terraform plan`
##### - `terraform apply --auto-approve`


### 4. Now, our cluster is up and running, you can visit the portal to see the subscription



## Set-up Kubernetes

### a. I used Use Terraform to automate the creation and configuration of my Kubernetes cluster. NB: Make sure to include all necessary resources for your application, such as Deployments, Services, and Ingresses.

![Kubernetes](/Snapshots/Screenshot%202024-08-11%20at%2020.43.40.png)

![kubernetes2](/Snapshots/Screenshot%202024-08-11%20at%2020.56.38.png)




### b. Deployment of the application

#### Deploying the microservice-based app on the cluster Having provisioned the AKS cluster and setup a connection to it on your terminal, you can now deploy your app. The application is a series of kubernetes files that creates a namespace, deploys pods for the different parts that make up the app and creates services that acts as network layers over the pods.

#### Do: 

`kubectl apply -f <name-of-manifest-file.yaml . -n <namespace>`

![manifest_apply](/Snapshots/deployed_sock-shop.png)


#### Once there are created, you can check that services are up and running.

#### you can check all running services using this command line

`kubectl get pods,svc -n sock-shop`

![running_services](/Snapshots/running_services.png)



### c. Install nginx and ingress controllers

### Install Ingress 

#### we need a more flexible and fine-grained control over incoming traffic. An Ingress is needed to give services in the cluster externally-reachable URLs, load balance traffic, terminate SSL/TLS, and offer name-based virtual hosting.

#### d. To use ingress, find the ingress.yaml file with the configurations and install an ingress controller in the cluster, here, the nginx ingress controller will be used.

#### e. to install our ingress-controller First do `helm repo search` (install helm if you haven’t done so already)

![search_repo](/Snapshots/Screenshot%202024-08-11%20at%2020.47.35.png)


#### f. Then add and install helm repo of your choice, I went with the nginx ingress controller

`helm install <repo_name> <nginx/nginx-ingress>`

![ingress](/Snapshots/Screenshot%202024-08-11%20at%2020.54.12.png)


#### g. next we check the running ingress controller `kubectl get pods,svc -n sock-shop` to see if the loadbalancer is displaying the external IP

![services](/Snapshots/Screenshot%202024-08-11%20at%2020.56.38.png)


#### h. now we can put it in our brower to see if the nginx is rendering properly and it will display an 404 error

![servives_nginx](/Snapshots/rendering_nginx.png)

### i. Next I link my loadbalancer Ip address to my domain 

#### - I login to my namecheap account, click on manage my domain name to connect my ip to the a panel of my domain name provider, for this exercise i used namecheap.com to create a domain for myself. 

#### - next i try my domain name on my browser see the display of my domain name

![domain](/Snapshots/rendering_domainname.png)


## Apply certificate using letsencrypt
### 1. the next step now is to apply for our let's encrypt certificate, and for us to be able to do that we must first install the "cert-manager", we would be using helm to add the jetstack repo, then we can now install the cert-manager and all its dependencies. we would be using these links "helm repo add jetstack `https://charts.jetstack.io --force-update`" and "`kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.2/cert-manager.yaml"`. After we apply this , all the dependencies would be installed as you can see below.

![cert-manger-created](/Snapshots/cert-managerinstalled.jpeg)
### 2. Also we create an `ingressissuer.yaml` file and a `certificate-manager.yaml` file

### 3. so we apply the issuer first followed by the certificate.yaml file

![cluster-issuer](/Snapshots/Cluster-Issuer.png)

![cert-manager](/Snapshots/Cert.png)

![cert](/Snapshots/created.png)

![cert_created](/Snapshots/created.png)

![cert_updated](/Snapshots/issued-certificate.png)

![cert_updated](/Snapshots/Screenshot%202024-08-14%20at%2012.49.31.png)

![cert_updated](/Snapshots/site_secured.jpeg)




## Install prometheus.....

### 1. First we do helm add and install prometheus repo 

#### - helm repo add prometheus-community `https://prometheus-community.github.io/helm-charts`

#### - `helm repo update`

#### - `helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace` (of ur choice)

### 2. add prometheus, grafana, alerts
#### - Next created my ingress file for prometheus, grafana, alerts and also create a Cname panel in my namecheap for my domain name.

![prometheus](/Snapshots/ingress4prometheus_.png)


![prometheus](/Snapshots/Screenshot%202024-08-11%20at%2023.05.55.png)

#### - Next I apply my ingress.yaml file as shown below using 

`kubectl apply -f ingress.yaml -n sock-shop`


![prometheus](/Snapshots/installed_prometheus.png)



#### - Next I test prometheus, grafana and alert on my browsser

![grafana](/Snapshots/grafana_displayed.png) 
### (-grafana)

![prometheus](/Snapshots/prometheus_displayed.png) 
### (-prometheus)

![alert](/Snapshots/alert-manager.png) 
### (-alert)



### 3. once we have successfully, updated our ingress yaml file with the correct service name and ports that both grafana and prometheus is running, Next, we Login into our grafana account; prometheus does not need log in.

###  4. the next step is to get the default username and password in order to be able to login into the grafana dashboard, and we will be using two different commands to do that. 

#### -First, is to get the grafana pod name using `"kubectl get pods -n sock-shop"`, 
#### -once we get the name, we would now use another commmand called `"kubectl describe pod <grafana pod name> -n sock-shop"`

#### -Immediately it is up and running, we look for the environment variable section, in order to find the secret name that stores both the password and the username. 


#### -Once, you see the secret name, next is to decode the details using this command 
##### >to get the username `"kubectl get secret prometheus-grafana -n <namespace> -o jsonpath="{.data.admin-user}" | base64 --decode`
##### > to the the password we use this code `kubectl get secret prometheus-grafana -n <namespace> -o jsonpath="{.data.admin-password}" | base64 --decode`

![generate_user_password](/Snapshots/user_passgenerated.png)

#### - the generated username and password is as shown below and behold we are ready to login into our grafana dashboard

![generate_user_password](/Snapshots/username_password.png)

### 5.- Next we Login to the grafana dashboard ussing the username and password and then it looks like the screenshot below 

![grafana_dashboard](/Snapshots/grafan_dasboard.png)


## We configure our grafana to send alert of notifications to our slack workspace.

### We can actually configure out alertmanager to send a notification whenever something goes wrong. so lets imagine we want to use Slack notification, you can set an alert rule on our grafana dashboard to do that. 


#### However, before we can configure this to happen, wee have to first create a workspace on slack or we use an old workspace where we are an admin of the slack workspace, 
![slak_workspace](/Snapshots/slack-workspace.png)

#### then you will need to install a webhook on the slack workspace, that way you will be provided with your slack webhook api, the webhook api would be needed for the alerting rule when you are setting it  up on the grafana dashboard, 
 
![slack_api](/Snapshots/slack-webhok-api.png)

#### so after succesfully setting it up, you can trigger it either by deleting a pod or anything and you will get a notification on your dedicated channel for the alert on your slack workspace

### below is the alert on my slack workspace as a change was occurring.

![slack_api](/Snapshots/Grafana-slack-alert.png)



## Sensitive Information: Using Ansible Vault to encrypt sensitive data, adding an extra layer of security.


### ansible-vault comes automatically with ansible package; if you dont have ansible installed, you can just install ansible using the following command: `brew install ansible` for mac-system.

![ansible-installed](/Snapshots/ansible-vault-iinstalled.png)

![ansible-installed](/Snapshots/ansible-vault%20encryption.png)


## Pipeline (CI/CD) using Github-action