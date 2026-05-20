# AWS VPC + EC2 Web Server Project

A hands-on AWS networking project that builds a complete network infrastructure from scratch and deploys a live web server on it.

---

## What I Built

A fully custom AWS network with a publicly accessible EC2 web server — no default VPC used.

## Architecture

```
Internet
   |
Internet Gateway
   |
VPC (10.0.0.0/16)
   |
Public Subnet (10.0.1.0/24)
   |
Route Table (0.0.0.0/0 → IGW)
   |
EC2 Instance (Apache Web Server)
   |
Security Group (Port 80 + Port 22)
```

---

## AWS Services Used

| Service | Purpose |
|---|---|
| VPC | Isolated private network |
| Subnet | Public subnet inside the VPC |
| Internet Gateway | Connects VPC to the internet |
| Route Table | Routes traffic from subnet to IGW |
| Security Group | Firewall — allows HTTP (80) and SSH (22) |
| EC2 | Virtual server running Apache web server |

---

## What I Learned

- How a VPC isolates your cloud resources in a private network
- How an Internet Gateway enables public internet access
- How Route Tables control where network traffic goes
- How Security Groups act as a firewall for EC2 instances
- How to install and run a web server (Apache) on EC2
- How to write Infrastructure as Code using CloudFormation

---

## How to Deploy

### Option 1 — AWS Console (Manual)

1. Create a VPC with CIDR `10.0.0.0/16`
2. Create a Public Subnet with CIDR `10.0.1.0/24`, enable auto-assign public IP
3. Create an Internet Gateway and attach it to the VPC
4. Create a Route Table, add route `0.0.0.0/0 → Internet Gateway`, associate with subnet
5. Launch an EC2 instance (Amazon Linux 2, t2.micro) in the public subnet
6. Attach a Security Group allowing port 80 (HTTP) and port 22 (SSH)
7. SSH in and run:
   ```bash
   sudo yum install httpd -y
   sudo systemctl start httpd
   echo "<h1>Hello from EC2!</h1>" | sudo tee /var/www/html/index.html
   ```
8. Visit `http://YOUR-EC2-PUBLIC-IP` in your browser

### Option 2 — CloudFormation (Automated)

Deploy the entire stack with one command:

```bash
aws cloudformation create-stack \
  --stack-name vpc-ec2-project \
  --template-body file://aws-vpc-ec2.yaml \
  --parameters \
      ParameterKey=KeyPairName,ParameterValue=YOUR_KEY_PAIR_NAME \
  --region us-east-1
```

After deployment, the **Outputs** tab in CloudFormation will show you the website URL.

---

## Files

```
├── aws-vpc-ec2.yaml    # CloudFormation template (entire infrastructure as code)
└── README.md           # This file
```

---

## Cleanup

To avoid AWS charges, delete the stack when done:

```bash
aws cloudformation delete-stack --stack-name vpc-ec2-project
```

Or manually terminate/delete all resources in the AWS Console.

---

## Author

Built as part of my AWS learning journey.
