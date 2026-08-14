# AWS Application Load Balancer – Path-Based Routing Project

## Overview
This project demonstrates how to route traffic to different backend applications using a single **Application Load Balancer (ALB)** based on the URL path. Requests to `/app1` are routed to one EC2 instance, and requests to `/app2` are routed to a separate EC2 instance — all within a highly available, multi-AZ VPC architecture.

## Architecture

```
                        Users
                          |
                          v
                 ┌──────────────────┐
                 │   ALB (10.10.0.0/16) │
                 │  Public Subnets (2 AZs) │
                 └──────────────────┘
                          |
              ┌───────────┴───────────┐
              |                       |
           /app1                   /app2
              |                       |
              v                       v
      ┌──────────────┐        ┌──────────────┐
      │ EC2-App1     │        │ EC2-App2     │
      │ 10.10.11.0/24│        │ 10.10.12.0/24│
      │ (AZ1)        │        │ (AZ2)        │
      └──────────────┘        └──────────────┘
```

## AWS Services Used
- **VPC** – Custom network (10.10.0.0/16)
- **Subnets** – 2 public subnets for ALB, 2 public subnets for EC2 instances (across 2 Availability Zones)
- **Internet Gateway** – Provides internet access to the VPC
- **Route Tables** – Routes traffic between subnets and the internet
- **Application Load Balancer (ALB)** – Routes traffic based on URL path
- **Target Groups** – Group EC2 instances by application (tg-app1, tg-app2)
- **EC2 Instances** – Amazon Linux servers running Apache (httpd), each hosting a different app
- **Security Groups** – Controls inbound traffic (HTTP, SSH)

## Steps Followed

1. Created a custom VPC (`10.10.0.0/16`)
2. Created 4 subnets across 2 Availability Zones (2 for ALB, 2 for EC2 instances)
3. Created and attached an Internet Gateway
4. Configured route tables and associated them with all subnets
5. Launched 2 EC2 instances (Amazon Linux), one per Availability Zone
6. Installed and configured Apache (httpd) on both instances
7. Created 2 Target Groups (`tg-app1`, `tg-app2`) and registered each EC2 instance
8. Created an Application Load Balancer spanning both public subnets
9. Configured listener rules on the ALB:
   - `/app1*` → forward to `tg-app1`
   - `/app2*` → forward to `tg-app2`
10. Tested routing by visiting the ALB's DNS name with `/app1` and `/app2` paths

## Apache Setup Script (used on each EC2 instance)

```bash
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd

# For App1 instance:
sudo mkdir -p /var/www/html/app1
echo "<h1>Hello from App 1 (Beginners)</h1>" | sudo tee /var/www/html/app1/index.html

# For App2 instance:
sudo mkdir -p /var/www/html/app2
echo "<h1>Hello from App 2 (Advanced)</h1>" | sudo tee /var/www/html/app2/index.html
```

## Issues Faced & How I Fixed Them

| Issue | Cause | Fix |
|---|---|---|
| `ERR_CONNECTION_TIMED_OUT` when accessing ALB | ALB security group didn't allow inbound HTTP (port 80) | Added inbound rule: HTTP, port 80, source 0.0.0.0/0 |
| `502 Bad Gateway` | EC2 instance had no web server running (user data script failed on launch) | Manually installed and started Apache (httpd) via SSH/EC2 Instance Connect |
| `404 Not Found` on `/app1` and `/app2` | Web page only existed at root (`/`), not matching the ALB's path-based routing rule | Created dedicated folders (`/var/www/html/app1`, `/var/www/html/app2`) with their own index pages |
| `/app2` still failing after fix | Accidentally connected to the wrong EC2 instance (App1 instead of App2) while troubleshooting | Verified the correct instance using `hostname -I` to confirm private IP matched the target subnet, then applied the fix on the correct instance |

## Result
Successfully implemented path-based routing using a single ALB endpoint, directing traffic to two separate backend applications across multiple Availability Zones — demonstrating both AWS networking fundamentals and real-world troubleshooting.

## Screenshots
See the `images/` folder for:
- ALB listener rules configuration
- Target group health status (Healthy)
- `/app1` and `/app2` successfully loading in browser
