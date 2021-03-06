# K8s_cluster-AWS-Dynamic_inventory-ANSIBLE
# Creating a K8s cluster with | Ansible on provisioned AWS instances | Dyanamic Inventory


## Amazon Web Service :
We can define AWS (Amazon Web Services) as a secured cloud services platform that offers compute power, database storage, content delivery, and various other functionalities. To be more specific, it is a large bundle of cloud-based services.

## Kubernetes:
Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

## Ansible :
Ansible is the simplest way to automate apps and IT infrastructure. Application Deployment + Configuration Management + Continuous Delivery.

# START
# I am going to configure a K8s Cluster on AWS, Ec-2 instances with a tool for automation called ansible. Also with a dynamic inventory.

## Why dynamic inventory?
#### For example, Aws default provides a dynamic IP so, after every restart, it's assigned to a new IP, or we launch a new O.S in a region called ap-south-1 and we need to use all the container as the database from that region and suppose there are 100 servers, Instead of putting it manually in the inventory there is a python script that works as a dynamic inventory which divides the instances with tags, region, subnets, etc.

# __First, let's start with provisioning of Ec-2 instances with ansible__
### Creating an Ansible role called provision-ec2

![Screenshot (311)](https://user-images.githubusercontent.com/64534620/107312833-2ec74b80-6a46-11eb-83c9-ec1f705aa10e.png)

### In roles, Creating a var file provision-ec2/vars/main.yml

![var](https://user-images.githubusercontent.com/64534620/107312952-7057f680-6a46-11eb-8aa6-a958c657fb3d.PNG)

### In the task/main.yml we are going to launch an ec2 instance using the amazon ami image one Master and two slaves.

![awsmain](https://user-images.githubusercontent.com/64534620/107313020-97162d00-6a46-11eb-9a69-0fb79118aa8a.PNG)


## Here, the most important are tags i.e k8s_master and k8s_slave because we are gonna use them in the dynamic inventory to separate them according to their uses.
## Here the access and secret key are pre-generated by me you can create your own as per your need, they are assigned to an IAM user.

![1612791874539](https://user-images.githubusercontent.com/64534620/107313690-e90b8280-6a47-11eb-8d53-a7a622f0b7cb.png)

## __Instead of providing the aws_access_key and aws_secret_key in the playbook, we can configure the AWS CLI and the Ansible will by default use the credentials which we provide while configuring the AWS CLI.__

## __The security group which was created with all inbound and outbound traffic named k8s cluster.  It's not good for security but set for doing this project.__
![3](https://user-images.githubusercontent.com/64534620/107313205-ecead500-6a46-11eb-8b5a-673f9947cb99.png)

### According to the role, all the tasks are successful

![5](https://user-images.githubusercontent.com/64534620/107313772-15270380-6a48-11eb-8b51-54580c3bc45a.png)
![4](https://user-images.githubusercontent.com/64534620/107313832-338cff00-6a48-11eb-9221-6f73e13101e3.png)
### Now the one instance for master and two for slaves are ready to configure for the k8s cluster.

## Now let's move to the dynamic inventory part
The dynamic inventory will separate the instances according to region, tags, public and private IPs, and many more

## We used two scripts for the dynamic inventory ec2.py and ec2.ini

https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.py  ---- ec2.py
https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.ini ---- ec2.ini

## Pre-requisites for these scripts are installing boto and boto3 in the system where you are running the program.
To install boto module
> pip3 install boto

> pip3 install boto3

## __To successfully make an API call to AWS, you will need to configure Boto (the Python interface to AWS). There are a variety of methods available, but the simplest is just to export two environment variables:__

>export AWS_ACCESS_KEY_ID='your access key'

>export AWS_SECRET_ACCESS_KEY='your secret key'

## or The second option is to copy the script to /etc/ansible/hosts and chmod +x it. You will also need to copy the ec2.ini file to /etc/ansible/ec2.ini. Then you can run ansible as you would normally.

![6](https://user-images.githubusercontent.com/64534620/107314539-aba7f480-6a49-11eb-8c57-54b3450b1b47.png)
## We have to only run ec2.py for getting the dynamic inventory.
## __The script separated the instances according to tags "tag_db_k8s_master" and "tag_db_k8s_slave" and made them a separated host group so we can use them in the playbook.__

# Our dynamic inventory is ready to use.

## Let's, Create a role for configuring the k8s master.
> ansible-galaxy init k8s-cluster

### In the k8s-cluster/vars/main.yml file
![var1](https://user-images.githubusercontent.com/64534620/107314768-3f79c080-6a4a-11eb-88b9-97c039a41336.PNG)

## Steps in the below playbook /k8s-cluster/tasks/main.yml

1. Installing docker and iproute-tc
2. Configuring the Yum repo for kubernetes
3. Installing kubeadm,kubelet kubectl program
4. Enabling the docker and Kubernetes
5. Pulling the config images
6. Confuring the docker daemon.json file
7. Restarting the docker service
8. Configuring the Ip tables and refreshing sysctl
9. Starting kubeadm service
10. Creating .kube Directory
11. Copying file config file
12. Installing Addons e.g flannel
13. Creating the token

![mmain1](https://user-images.githubusercontent.com/64534620/107314921-8cf62d80-6a4a-11eb-8888-39b871e49c39.PNG)
![mmain2](https://user-images.githubusercontent.com/64534620/107314926-8d8ec400-6a4a-11eb-9e79-5ee474c77c48.PNG)

### The file created for running the role(k8s-cluster)

![project](https://user-images.githubusercontent.com/64534620/107315032-ba42db80-6a4a-11eb-9a52-cbb03671594f.PNG)

## Let's, Configure the slave on the other two ec-2 instances.
### Create a role k8s-slave
> ansible-galaxy init k8s-slave

### In /k8s-slave/vars/main.yml

![var1](https://user-images.githubusercontent.com/64534620/107314768-3f79c080-6a4a-11eb-88b9-97c039a41336.PNG)

## Steps in the below playbook /k8s-slave/task/main.yml

1. Installing docker and iproute-tc
2. Configuring the Yum repo for kubernetes
3. Installing kubeadm,kubelet kubectl program
4. Enabling the docker and kubenetes
5. Pulling the config images
6. Confuring the docker daemon.json file
7. Restarting the docker service
8. Configuring the Ip tables and refreshing sysctl
![smain1](https://user-images.githubusercontent.com/64534620/107315216-173e9180-6a4b-11eb-9dab-f070389f346b.PNG)

The file created for running the role(k8s-slave)

![project](https://user-images.githubusercontent.com/64534620/107315032-ba42db80-6a4a-11eb-9a52-cbb03671594f.PNG)

## Checking if the cluster is working or not :
### As in the above-created token in master join the slaves
### Adding this token in both the slaves
![11](https://user-images.githubusercontent.com/64534620/107316220-0e4ebf80-6a4d-11eb-9631-3fe4144a69e5.png)

### After running "kubectl get nodes" in the Master.
![13](https://user-images.githubusercontent.com/64534620/107316381-5968d280-6a4d-11eb-85ac-037e6793c88f.png)

### Let's launch a pod on this cluster
> kubectl create deployment success --image=vimal13/apache-webserver-php
![14](https://user-images.githubusercontent.com/64534620/107316498-9339d900-6a4d-11eb-96b3-19ee322b40b9.png)
### Exposing the POD
> kubectl expose deployments success --type=NodePort --port=80

> kubectl get pods -o wide
![16](https://user-images.githubusercontent.com/64534620/107316559-b8c6e280-6a4d-11eb-949c-391dad5cca46.png)

## The pod is launched in the first slave

![17](https://user-images.githubusercontent.com/64534620/107316594-cf6d3980-6a4d-11eb-819e-531691e67194.png)

# HAPPY LEARNING ⭐⭐⭐





