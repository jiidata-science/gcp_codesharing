## Instructions for creating a VM on GCP, installing & launching RStudio

### Useful links / comments / prerequisites

Pre-requisites : Download and follow the installation instructions for the Google Cloud SDK command line utility, gcloud here (https://cloud.google.com/sdk/)
Useful link: https://yuhuisdatascienceblog.blogspot.com/2017/07/setting-up-r-studio-server-on-google.html
Context:
- a VM is just an emulation of a computer running inside another (bigger) computer
- Google Compute Engine is part of the Google Cloud Platform and delivers high-performance, rapidly scalable VMs
- A new VM can be deployed or shut down within seconds, while existing VMs can easily be ramped up or down (cores added, RAM added, etc.)
- It’s possible to complete nearly all of the steps in this guide via the Compute Engine browser console. However, we’ll stick with the gcloud command line utility (which you should have installed already), because that will make it easier to document our steps and will also save us some headaches further down the road. For example, when it comes reproducibility across machines and perhaps deployments to new environments that require setup
Other useful links / tips :
- Google pricing calculator : https://cloud.google.com/products/calculator/

## STEP 01 : CONFIGURE ENVIRONMENT/PROJECT VARIABLES

Note: the following should be run via cloud console or through your local computer, connected to the project via `gcloud init`

```shell
# set session parameters
project_id=<project_id> # id for target GCP project
processing_region=<europe-west4> # processing region
processing_zone=<europe-west4-a> # processing zone
vm_name=<rstudio-test-2> # unique name of compute engine to create

# run gcloud configuration to ensure execution to correct project and execution zone
gcloud config set project $project_id 
gcloud config set compute/zone $processing_region
```

## STEP 02 : CREATE VIRTUAL MACHINE

```shell
# create a compute engine within the project
# machine type standard-8 provides 8 CPUs with 30GB RAM (select based on size of data you're working with)
gcloud compute instances create $vm_name \
    --image-family ubuntu-1804-lts \
    --image-project ubuntu-os-cloud  \
    --machine-type n1-standard-8 \
    --zone $processing_zone
```
- You should see the compute engine appear in the GCP Console within the Compute Engine Section
- RStudio Server will run on port 8787 of the External IP
- We will use port forwarding to local machine as your personal credentials + SDK are sufficient so you can access RStudio via your local browser

## STEP 03 : CONNECT / SSH INTO VIRTUAL MACHINE

- Wait for the VM to be created. You should see this in the Console
- Then login / SSH into machine
```shell
gcloud compute ssh $vm_name --zone $processing_zone
```

## STEP 04 : INSTALL R, RSTUDIO AND BASIC DEPENDENCIES

```shell
# get latest packages from ubuntu
# -y argument indicates no prompt required
sudo apt-get -y update
sudo apt-get -y upgrade

# installing base R
sudo apt-get -y install r-base r-base-dev

# install R Studio Server
# I installed R Studio Server 1.0.153 but you can reference later version available here :
sudo apt-get -y install gdebi-core
wget https://download2.rstudio.org/rstudio-server-1.0.153-amd64.deb
sudo dpkg -i rstudio-server-1.0.153-amd64.deb

# installing supporting packages
# these are usually required to install R packages inside R Studio Server.
sudo apt-get -y install libcurl4-openssl-dev libssl-dev libxml2-dev
```

## STEP 05 : CREATE USER GROUPS FOR R

```shell
# create a user group
sudo addgroup datascientists

# this user will be assigned full access to all files (no specific user rights)
default_pw=xxx # created with password : xxx
default_user=master
sudo adduser --disabled-password --gecos "" $default_user
echo -e "$default_pw\n$default_pw" | (sudo passwd -q $default_user)
  
# Create the shared folder
cd /home/master
sudo mkdir shared_folder
sudo chown -R master:datascientists shared_folder/
sudo chmod -R 770 shared_folder/
```

## STEP 06 : CONNECT TO VM AND LAUNCH RSTUDIO IN LOCAL BROWSER

```shell
# this is where you need SDK installed on your local machine and access to the project
# note : once the following command is executed, go to your browser and launch : localhost:8787
# username : master, password: xxx
gcloud compute --project "$project_id" ssh --zone "$processing_zone" "$vm_name" -- -L localhost:8787:localhost:8787
```


