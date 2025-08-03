# Java Microservices on Vagrant VMs

This project demonstrates how to deploy a **multi-tier Java microservices application** on separate Virtual Machines using **Vagrant** and **VirtualBox**.

## Architecture
The application architecture is composed of 5 microservices, each one will be deployed on a separate VM, they are:

| Service         | Technology             | VM Hostname |
|-----------------|------------------------|-------------|
| Load Balancer   | Nginx                  | `web01`     |
| Application     | Java 11 + Maven + Tomcat| `app01`     |
| Database        | MariaDB                | `db01`      |
| Cache           | Memcached              | `mc01`      |
| Message Queue   | RabbitMQ                | `rmq01`     |

## Requirements
- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
