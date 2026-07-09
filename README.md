### AWS DevOps Intern Assignment — EC2 + Nginx Website Deployment

A static website deployed on an Ubuntu EC2 instance behind Nginx, provisioned and configured end to end: instance launch, Security Group rules, Linux server administration, website deployment, and two bonus tasks (Elastic IP, Docker). The instance has since been terminated; screenshots below are the evidence of the working deployment.

#### Summary

| Task | Description | Status |
|---|---|---|
| 1. EC2 Setup | Ubuntu instance, Security Group, SSH access | Done |
| 2. Linux Basics | Package updates, Nginx install, service/resource checks | Done |
| 3. Website Deployment | Custom HTML page replacing the Nginx default | Done |
| 4. Git & GitHub | Repository with source, docs, and screenshots | Done |
| 5. Documentation | PDF report — services, commands, problems, learnings | Done |
| Bonus: Elastic IP | Fixed public IP attached to the instance | Done |
| Bonus: Docker | Docker Engine installed, hello-world verified | Done |

#### Task 1: EC2 Instance Setup

Launched an Ubuntu EC2 instance and opened SSH and HTTP access through the Security Group.
```
Instance ID  : i-0bb5b8f567a079619
Instance type: t3.micro
Region       : ap-south-1 (Asia Pacific - Mumbai)
AMI          : Ubuntu
VPC          : vpc-0b1429f9929102e33
Subnet       : subnet-0455aedc3d07a3b0f
Public IPv4  : 15.252.44.139 (Elastic IP)
Public DNS   : ec2-15-252-44-139.ap-south-1.compute.amazonaws.com
```

Security Group inbound rules: TCP 22 (SSH) and TCP 80 (HTTP) from `0.0.0.0/0`.

Private key permissions must be restricted before SSH will accept the key.
```
chmod 400 key.pem
ssh -i key.pem ubuntu@15.252.44.139
```

![EC2 instance summary](ec2-dashboard.png)

![SSH login to the EC2 instance](ssh-login-terminal.png)

#### Task 2: Linux Basics and Nginx Installation

Updated packages, installed Nginx, and verified the service and system resources.
```
sudo apt update
sudo apt install nginx -y
sudo systemctl status nginx.service
sudo systemctl restart nginx.service
ss -tulpn
curl ifconfig.me
df -h
free -h
ps aux
```

![Nginx systemctl status showing active running](nginx-status.png)

![Disk usage via df -h and memory usage via free -h](disk-memory-usage.png)

#### Task 3: Website Deployment

Nginx's default config (`/etc/nginx/sites-available/default`) serves from `/var/www/html` and prefers `index.html` over the stock `index.nginx-debian.html`, so the custom page was dropped in as `index.html` without touching the original default file.
```
sudo vim /var/www/html/index.html
sudo systemctl restart nginx.service
```

![Student details page served by Nginx](nginx-info-page.png)

#### Task 4: Git and GitHub

All source, documentation, and evidence are version-controlled in this repository.
```
index.html                 — website source deployed to /var/www/html/index.html
README.md                  — this file
linux-basics-nginx.md      — full command reference and troubleshooting notes
docker-installation.md     — Docker Engine apt-repository install reference
task5-documentation.md     — Task 5 report source
task5-documentation.pdf    — Task 5 report, submission-ready
```

#### Task 5: Documentation

Full write-up covering AWS services used, Linux commands, problems faced, learnings, and total time taken: [task5-documentation.pdf](task5-documentation.pdf).

#### Bonus: Elastic IP

Allocated an Elastic IP and associated it with the instance so the public IP stays fixed across stop/start cycles.
```
Elastic IP     : 15.252.44.139
Allocation ID  : eipalloc-0369776a8ce2ee10a
```

![Elastic IP associated successfully](elastic-ip.png)

![EC2 instance summary showing the Elastic IP](ec2-with-elasticip.png)

#### Bonus: Docker

Installed Docker Engine via the official apt repository (full steps in `docker-installation.md`) and verified the install.
```
sudo systemctl status docker.service
sudo docker run hello-world
```

![Docker service active and running](docker-status.png)

![docker run hello-world output](docker-hello-world.png)

#### Further Reference

`linux-basics-nginx.md` has the complete command list with explanations and a troubleshooting section covering SSH key permissions, SSH connection drops, and Security Group checks.
