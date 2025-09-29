# AWS-Network-Load-Balancer-Configuration

## Overview

This project demonstrates the complete setup and configuration of a Network Load Balancer (NLB) in AWS, including environment preparation, load balancer creation, and performance monitoring. The lab showcases practical skills in AWS networking, load balancing, and infrastructure management.

## Project Architecture

The implementation consists of:
- **VPC Configuration**: Custom subnet creation across multiple Availability Zones
- **Network Load Balancer**: Internet-facing NLB distributing traffic across EC2 instances
- **EC2 Web Servers**: Two instances running Apache HTTP Server in different AZs
- **Network Security**: Configured Network ACLs and Security Groups
- **Monitoring**: CloudWatch metrics integration for traffic analysis

## Key Components

### Infrastructure Setup
- **Region**: US-East-1 (N. Virginia)
- **Subnets**: 
  - PublicA (us-east-1a) - 10.0.1.0/24
  - PublicB (us-east-1b) - 10.0.2.0/24
- **EC2 Instances**: 2x t3.micro instances running Amazon Linux 2
- **Load Balancer Type**: Network Load Balancer (Layer 4)

### Network Configuration
- Internet Gateway attached to VPC
- Custom route tables for public subnet access
- Network ACL rules for HTTP, HTTPS, SSH, and ephemeral ports
- Security Groups for controlled instance access

## Implementation Steps

### 1. Environment Preparation
- Created PublicB subnet in us-east-1b with CIDR block 10.0.2.0/24
- Configured route table with internet gateway association
- Updated Network ACL with inbound rules for web traffic and SSH access

### 2. EC2 Instance Deployment
Launched two web servers with automated configuration using user data scripts:

**WebA Instance** (us-east-1a):
```bash
#!/bin/bash
yum update -y && yum -y install httpd && systemctl enable httpd && systemctl start httpd
usermod -a -G apache ec2-user
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
echo "Request Handled by: WebA" >> /var/www/html/index.html
```

**WebB Instance** (us-east-1b):
```bash
#!/bin/bash
yum update -y && yum -y install httpd && systemctl enable httpd && systemctl start httpd
usermod -a -G apache ec2-user
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
echo "Request Handled by: WebB" >> /var/www/html/index.html
```

**Script Functions:**
- Updates system packages and installs Apache HTTP Server
- Enables and starts httpd service for automatic startup
- Configures proper user groups and file permissions
- Creates custom index.html to identify which instance handled the request

### 3. Network Load Balancer Configuration
- Created internet-facing NLB named `NLB4LAB`
- Configured target group `nlbTargets` with TCP protocol on port 80
- Registered both WebA and WebB instances as targets
- Enabled TCP health checks for target monitoring

### 4. Testing and Monitoring
- Load tested the NLB using curl in a continuous loop
- Monitored traffic distribution across both instances
- Analyzed CloudWatch metrics showing traffic spikes and distribution patterns

## Technical Skills Demonstrated

- **AWS VPC Networking**: Subnet creation, route tables, internet gateway configuration
- **Network Security**: Network ACL rules, security group management
- **Load Balancing**: NLB setup, target group configuration, health checks
- **EC2 Management**: Instance launching, user data scripts, automated configuration
- **Linux Administration**: Bash scripting, Apache configuration, file permissions
- **Monitoring & Testing**: CloudWatch metrics, CLI-based load testing
- **High Availability**: Multi-AZ deployment for fault tolerance

## Load Testing Results

The load balancer successfully distributed traffic between both instances, demonstrating:
- Even distribution of requests across WebA and WebB
- Healthy target status maintained during high traffic
- Real-time metrics visible in CloudWatch dashboards
- Seamless failover capability across Availability Zones

## Technologies Used

- **AWS Services**: VPC, EC2, Network Load Balancer, CloudWatch
- **Operating System**: Amazon Linux 2
- **Web Server**: Apache HTTP Server
- **Scripting**: Bash
- **Tools**: AWS CLI, curl, SSH

## Key Learnings

- Network Load Balancers operate at Layer 4 (Transport Layer) and are ideal for TCP/UDP traffic
- Multi-AZ deployment provides high availability and fault tolerance
- Proper Network ACL configuration is essential for traffic flow
- Health checks ensure traffic only routes to healthy instances
- CloudWatch provides valuable insights into load balancer performance

## Prerequisites

- AWS Account with appropriate IAM permissions
- Basic understanding of VPC networking concepts
- Familiarity with EC2 and load balancing principles
- SSH client for remote instance access

## Security Considerations

- Network ACL configured with minimal required ports (80, 443, 22, ephemeral)
- Security Groups provide instance-level traffic control
- No key pairs stored for instances (password authentication used for demo)
- Public subnets used for demonstration; production would include private subnets

## Future Enhancements

- Implement Auto Scaling Groups for dynamic capacity management
- Add SSL/TLS termination for HTTPS traffic
- Deploy application in private subnets with NAT Gateway
- Implement WAF (Web Application Firewall) for additional security
- Set up automated backups and disaster recovery procedures
- Configure cross-region replication for enhanced availability

## Conclusion

This hands-on lab successfully demonstrates the configuration of a fully functional Network Load Balancer in AWS, showcasing practical skills in cloud networking, load balancing, and infrastructure management. The implementation provides a foundation for building scalable, highly available web applications in AWS.

---

**Note**: This project was completed as part of AWS hands-on lab training in the N. Virginia (us-east-1) region.
