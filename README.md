# Deploy Java Microservices Application on VMs using Vagrant

## Introduction
This project demonstrates how to deploy a **multi-tier Java microservices application** on separate virtual machines using **Vagrant** IaC tool and **VirtualBox**.

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
- Install VirtualBox.
- Install Vagrant.
- Clone the repository.
- Install Vagrant plugins.
  ```bash
  vagrant plugin install vagrant-hostmanager

- Start creating VMs:
  ```bash
  vagrant up

- Access each VM via SSH:
  ```bash
  vagrant ssh web01
  vagrant ssh app01
  vagrant ssh rmq01
  vagrant ssh mc01
  vagrant ssh db01

## VMs Provisioning
### Database (db01)
  
### Cache (mc01)
  
### Message Queue (rmq01)
  
### Application (app01)
  
### Load Balancer (web01)

## Packaging Vagrant Boxes
- Packaging each VM, i.e., converting it to a Vagrant Box:
  ```bash
    vagrant package db01 --output web01.box
    vagrant package mc01 --output mc01.box
    vagrant package rmq01 --output rmq01.box
    vagrant package app01 --output app01.box
    vagrant package web01 --output web01.box

## Uploading Boxes to Vagrant Cloud
- Log in to Vagrant Cloud.
- Upload the ".box" file for each VM.

### Built Boxes
- [web01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/web01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [app01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/app01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [rmq01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/rmq01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [mc01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/mc01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)
- [db01](https://portal.cloud.hashicorp.com/services/vagrant/registries/multi-tier-java-web-app-project1-boxes/boxes/db01?project_id=f92dac97-833c-445f-b97f-2d0948d624c9)

