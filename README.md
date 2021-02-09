# K8s_cluster-AWS-Dynamic_inventory-ANSIBLE
# Creating a K8s cluster with | Ansible on provisioned AWS instances | Dyanamic Inventory


## Amazon Web Service :
We can define AWS (Amazon Web Services) as a secured cloud services platform that offers compute power, database storage, content delivery, and various other functionalities. To be more specific, it is a large bundle of cloud-based services.

## Kubernetes:
Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

## Ansible :
Ansible is the simplest way to automate apps and IT infrastructure. Application Deployment + Configuration Management + Continuous Delivery.

# START
# In this article, I am going to configure a K8s Cluster on AWS, Ec-2 instances with a tool for automation called ansible. Also with a dynamic inventory.

## Why dynamic inventory?
### For example, Aws default provides a dynamic IP so, after every restart, it's assigned to a new IP, or we launch a new O.S in a region called ap-south-1 and we need to use all the container as the database from that region and suppose there are 100 servers, Instead of putting it manually in the inventory there is a python script that works as a dynamic inventory which divides the instances with tags, region, subnets, etc.

# __First, let's start with provisioning of Ec-2 instances with ansible__
### Creating an Ansible role called provision-ec2

![GitHub Logo](I:\ARTH\Kubernetes\Tassk\Task19\Images\carbon.sh\mmain1.PNG)








