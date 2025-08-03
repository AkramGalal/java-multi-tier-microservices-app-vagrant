# Deploy Java Microservices Application on VMs using Vagrant

This project demonstrates how to deploy a **multi-tier Java microservices application** on separate Virtual Machines using **Vagrant** IaC tool and **VirtualBox**.

## Architecture
The application architecture is composed of 5 microservices, each one will be deployed on a separate VM, they are:

| VM         | Technology             | VM Hostname |
|-----------------|------------------------|-------------|
| Load Balancer   | Nginx                  | `web01`     |
| Application     | Java 11 + Maven + Tomcat| `app01`     |
| Message Queue   | RabbitMQ                | `rmq01`     |
| Cache           | Memcached              | `mc01`      |
| Database        | MariaDB                | `db01`      |


## Requirements
- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- Vagrant plugins.

## Setup and Deployment

### VMs Provisioning

#### Database (db01)

#### Cache (mc01)

#### Message Queue (rmq01)

#### Application (app01)

#### Load Balancer (web01)
