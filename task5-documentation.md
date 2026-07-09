### AWS DevOps Intern Assignment — Documentation Report

#### Candidate Details

Name: Rahul Kumar
College: Vellore Institute of Technology
Branch: Computer Science
Email: rahulthakur.developer@gmail.com
Date: 09/07/2026

#### AWS Services Used

##### Amazon EC2

Ubuntu instance used to host Nginx and Docker.
```
Instance ID   : i-0bb5b8f567a079619
Instance type : t3.micro
Region        : ap-south-1 (Asia Pacific - Mumbai)
AMI           : Ubuntu
```

##### VPC and Subnet

Default VPC networking used for the instance.
```
VPC    : vpc-0b1429f9929102e33
Subnet : subnet-0455aedc3d07a3b0f
```

##### Security Groups

Inbound rules opened for SSH and HTTP access.
```
TCP 22 (SSH) from 0.0.0.0/0
TCP 80 (HTTP) from 0.0.0.0/0
```

##### Elastic IP (Bonus)

Allocated and associated to keep the public IP fixed across instance stop/start.
```
Elastic IP      : 15.252.44.139
Allocation ID   : eipalloc-0369776a8ce2ee10a
```

#### Linux Commands Used

Restrict the SSH private key to owner-read-only.
```
chmod 400 key.pem
```

Connect to the instance.
```
ssh -i key.pem ubuntu@15.252.44.139
```

Update packages and install Nginx.
```
sudo apt update
sudo apt install nginx -y
```

Check and restart the Nginx service.
```
sudo systemctl status nginx.service
sudo systemctl restart nginx.service
```

Check listening ports.
```
ss -tulpn
```

Check the instance's public IP.
```
curl ifconfig.me
```

Check disk and memory usage.
```
df -h
free -h
```

Check running processes.
```
ps aux
```

Inspect the Nginx site config and document root.
```
cat /etc/nginx/sites-available/default
```

Deploy the custom website page.
```
sudo vim /var/www/html/index.html
sudo systemctl restart nginx.service
curl http://localhost
```

Install Docker Engine via the apt repository (see `docker-installation.md` for the full command sequence), then verify.
```
sudo systemctl status docker.service
sudo docker run hello-world
```

#### Problems Faced

##### Browser auto-switches from HTTP to HTTPS

The browser occasionally forced `https://15.252.44.139` instead of `http://`, and the connection failed because Nginx only listens on port 80 — no SSL/443 is configured on this instance. Cause: browser "always use secure connections" / HSTS-style upgrade behavior on repeat visits. Fix: type `http://` explicitly in the address bar, or open the site in a private/incognito window.

##### SSH key rejected: UNPROTECTED PRIVATE KEY FILE

SSH refused the `.pem` key on first connect because its file permissions were too open. Fixed with `chmod 400 key.pem` before connecting.

##### SSH session dropped mid-edit

`vim` was interrupted by `client_loop: send disconnect: Broken pipe` during an edit, caused by a network/idle interruption rather than an EC2 or Nginx fault. No changes were lost since the file had not been saved yet.

#### Learnings

Provisioning and securing an EC2 instance: launching Ubuntu, configuring Security Group rules for SSH and HTTP, and connecting safely with a permission-restricted key.

Linux service administration: managing Nginx with `systemctl`, checking disk/memory usage, and identifying listening ports with `ss`.

Nginx fundamentals: document root configuration and `index.html` precedence over the stock `index.nginx-debian.html` for overriding the default page.

Elastic IP usage: keeping a stable public IP across instance stop/start cycles, and updating references after the IP changes.

Docker Engine installation via the official apt repository, and verifying an install with `docker run hello-world`.

Git/GitHub workflow for versioning and submitting infrastructure documentation alongside the deployed website source.

#### Total Time Taken

Approximately 30 minutes.
