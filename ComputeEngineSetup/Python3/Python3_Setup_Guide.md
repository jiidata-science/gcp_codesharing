## Instructions for creating a VM on GCP, installing & launching Jupyter notebooks (Python3)

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
- create a compute engine within the project
- machine type standard-4 provides 4 CPUs with 15GB RAM (select based on size of data you're working with)
- The harware tier of the vm is reconfigurable at any point
- Useful link for machine types : https://cloud.google.com/compute/docs/machine-types
- Useful link for image-families : https://cloud.google.com/compute/docs/images
```shell
gcloud compute instances create $vm_name \
    --image-family debian-9 \
    --image-project debian-cloud  \
    --machine-type n1-standard-4 \
    --zone $processing_zone
```

## STEP 03 : SSH / CONNECT TO VIRTUAL MACHINE & INSTALL REQUIRED PACKAGES

- connect to virtual machine via cloud shell or local machine
```shell
gcloud compute ssh $vm_name --zone $processing_zone
```

- setup OS and Python3 environment
```shell
sudo apt-get update && sudo apt-get install -y \
    python3-pip \
    python3-dev \
    build-essential \
    libssl-dev \
    libffi-dev \
    maven \
    openjdk-8-jdk \
    python-tk \
    git
```

- install Python packages

```shell
pip3 install --upgrade pip
sudo pip3 install flask \
        gcloud \
        jupyter \
        numpy \
        pandas \
        tensorflow --upgrade \
        keras \
        h5py \
        scikit-learn \
        imbalanced-learn \
        matplotlib \
        seaborn \
        dask \
        toolz \
        cloudpickle \
        py4j \
        pandas-profiling \
        pandas-gbq \
        jupyterlab \
        xgboost
```

## STEP 04 : (RE-SSH OR RE-CONNECT TO VIRTUAL MACHINE &) LAUNCH JUPYTER NOTEBOOKS IN BROWSER
- Jupyter notebook should run on port 8888 by default (note: this can be configured in the jupyter config file)
- Run the following command in local machine terminal to connect to compute engine and port forward to local browser
```shell
gcloud compute --project "$project_id" ssh --zone "$processing_zone" "$vm_name" -- -L localhost:8080:localhost:8080
```
- Once you've accessed the virtual machine, run the following command to launch a jupyter notebooks session
```shell
jupyter notebook --port 8080
```

- Navigate to your local browser and enter the following URL to launch Jupyter notebooks:Go 
```shell
http://localhost:8080
```







