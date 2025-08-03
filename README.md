# Deploy Java Microservices Application on VMs using Vagrant

## Introduction
This project demonstrates how to deploy a **multi-tier Java microservices application** on separate Virtual Machines using **Vagrant** IaC tool and **VirtualBox**.

<img width="627" height="303" alt="image" src="https://github.com/user-attachments/assets/fdde26cf-ab8d-4fbc-ae52-6b9b440aa075" />

## Architecture
The application architecture is composed of 5 microservices, each one will be deployed on a separate VM, they are:

| VM         | Technology             | VM Hostname |
|-----------------|------------------------|-------------|
| Load Balancer   | Nginx                  | `web01`     |
| Application     | Java 11 + Maven + Tomcat| `app01`     |
| Message Queue   | RabbitMQ                | `rmq01`     |
| Cache           | Memcached              | `mc01`      |
| Database        | MariaDB                | `db01`      |

<img width="2048" height="1302" alt="image" src="https://github.com/user-attachments/assets/8d9c68fa-2f3c-4a95-b9e6-da550952c2be" />

## Requirements
- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- Vagrant plugins.

## Setup and Deployment
- Clone the repository
- Install Vagrant plugins

vagrant plugin install vagrant-hostmanager

- Start creating VMs:
vagrant up'

- Access each VM via SSH:
vagrant ssh web01
vagrant ssh app01
vagrant ssh rmq01
vagrant ssh mc01
vagrant ssh db01

### VMs Provisioning

#### Database (db01)

#### Cache (mc01)

#### Message Queue (rmq01)

#### Application (app01)

#### Load Balancer (web01)

o	To package a VM, i.e., converting it to a Vagrant Box, navigate to the project directory and start packaging each VM as follows:
	# vagrant package db01 --output web01.box
	# vagrant package mc01 --output mc01.box
o	It is worth noting that the packing process will force shutting down the VM.
o	Now, after you have the “.box” file of each VM, we will upload these files on Vagrant Cloud account.
o	From Vagrant Cloud account, create a box, define it, add a version, add a provider (for instance: VirtualBox), upload the image/box file, then release it.
