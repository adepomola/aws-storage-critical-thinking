# AWS Critical Thinking Project

## Project Overview

This project demonstrates the design and implementation of three AWS cloud solutions that address different real-world business challenges. The solutions focus on improving application performance, hosting static websites, and modernizing legacy infrastructure using AWS cloud services and cloud-native architectural principles.

The project covers:

- Multi-region infrastructure deployment for low-latency access.
- Static website hosting using Amazon S3.
- A hybrid cloud architecture for migrating legacy applications while maintaining an on-premises database.

---

## Project Objectives

The objectives of this project are to:

- Design a highly available multi-region AWS infrastructure.
- Improve application performance for geographically distributed users.
- Host a static website using Amazon S3.
- Secure cloud resources using AWS best practices.
- Design a cloud-native hybrid architecture for legacy applications.
- Demonstrate the use of AWS services through practical implementation.

---

## AWS Services Used

The following AWS services were used throughout this project:

- Amazon EC2
- Amazon VPC
- Application Load Balancer (ALB)
- Amazon CloudFront
- Amazon S3
- AWS Identity and Access Management (IAM)
- Security Groups
- Internet Gateway
- Route 53 (Architecture Design)
- Site-to-Site VPN (Architecture Design)
- Auto Scaling Group (Architecture Design)

---

# Scenario 1

## Problem Statement

As a Junior DevOps Engineer, the objective is to design and implement an AWS infrastructure that provides users located in *India* and *London* with a fast browsing experience while minimizing latency.

The solution should make use of AWS global infrastructure, content delivery, load balancing, and multi-region deployment strategies.

---

## Architecture Diagram

![Scenario 1 Architecture](diagrams/scenario1-architecture.png)

---

## Solution Architecture

The solution consists of:

- Users located in India and London
- Amazon CloudFront
- Application Load Balancer
- Amazon EC2 instance deployed in Mumbai Region (ap-south-1)
- Amazon EC2 instance deployed in London Region (eu-west-2)
- Amazon VPC
- Internet Gateway

CloudFront caches content closer to end users while the Application Load Balancer distributes incoming traffic to available EC2 instances, ensuring high availability and low latency.

---

# Scenario 1: Multi-Region Infrastructure for Low Latency

## Implementation Steps

### Step 1: Create the Mumbai EC2 Instance

1. Open the AWS Management Console.
2. Navigate to *EC2 Dashboard*.
3. Ensure the selected region is *Asia Pacific (Mumbai) – ap-south-1*.
4. Launch a new Ubuntu EC2 instance.
5. Select the Free Tier eligible instance type (t2.micro).
6. Create or select an existing key pair.
7. Configure the Security Group to allow:
   - SSH (Port 22)
   - HTTP (Port 80)
8. Launch the instance.

*Screenshot*

![Mumbai EC2 Instance](screenshots/scenario1/scenario1-01-mumbai-ec2.png)

---

### Step 2: Create the London EC2 Instance

1. Change the AWS Region to *Europe (London) – eu-west-2*.
2. Launch another Ubuntu EC2 instance.
3. Select the same instance type.
4. Configure the Security Group.
5. Launch the instance.

*Screenshot*

![London EC2 Instance](screenshots/scenario1/scenario1-02-london-ec2.png)

---

### Step 3: Configure the Security Group

Configure the Security Group to allow inbound traffic.

| Protocol | Port | Purpose |
|----------|------|----------|
| SSH | 22 | Remote Administration |
| HTTP | 80 | Web Traffic |

*Screenshot*

![Security Group](screenshots/scenario1/scenario1-03-security-group.png)

---

### Step 4: Install Apache Web Server

Connect to each EC2 instance using SSH.

Update Ubuntu packages.

```bash
sudo apt update
```

Install Apache.

```bash
sudo apt install apache2 -y
```

Enable Apache.

```bash
sudo systemctl enable apache2
```

Start Apache.

```bash
sudo systemctl start apache2
```

Verify the service.

```bash
sudo systemctl status apache2
```

---

### Step 5: Create Custom Web Pages

For the Mumbai server:

```bash
echo "<h1>Welcome from Mumbai Server</h1>" | sudo tee /var/www/html/index.html
```

For the London server:

```bash
echo "<h1>Welcome from London Server</h1>" | sudo tee /var/www/html/index.html
```

---

### Step 6: Create an Application Load Balancer

1. Open the EC2 Console.
2. Navigate to *Load Balancers*.
3. Click *Create Load Balancer*.
4. Select *Application Load Balancer*.
5. Configure:
   - Internet-facing
   - IPv4
   - HTTP Listener (Port 80)
6. Select the VPC.
7. Select the public subnets.
8. Create a Target Group.
9. Register both EC2 instances.
10. Create the Load Balancer.

*Screenshot*

![Application Load Balancer](screenshots/scenario1/scenario1-04-load-balancer.png)

---

### Step 7: Configure Amazon CloudFront

1. Open Amazon CloudFront.
2. Create a new Distribution.
3. Select the Application Load Balancer as the Origin.
4. Leave the default cache settings.
5. Create the distribution.

*Screenshot*

![CloudFront Distribution](screenshots/scenario1/scenario1-05-cloudfront.png)

---

## Result

The deployed infrastructure allows users from India and London to access the application with reduced latency through CloudFront and the Application Load Balancer. Requests are automatically routed to the available backend servers, improving performance, availability, and scalability.

---

## Scenario 1 Architecture

![Scenario 1 Architecture](diagrams/scenario1-architecture.png)

---

## AWS Services Used

- Amazon EC2
- Amazon VPC
- Security Groups
- Application Load Balancer
- Amazon CloudFront
- Internet Gateway

# Scenario 2: Hosting a Static Website Using Amazon S3

## Problem Statement

As a Junior DevOps Engineer, the objective is to host a static website using Amazon S3. The solution should include configuring an S3 bucket for static website hosting, uploading website files, configuring permissions, and enabling website hosting. The architecture should also consider CDN integration using Amazon CloudFront to improve performance and reduce latency.

---

## Architecture Diagram

![Scenario 2 Architecture](diagrams/scenario2-architecture.png)

---

## Solution Architecture

The solution consists of:

- Users
- Internet
- Amazon CloudFront (Optional)
- Amazon S3 Bucket
- Static Website Hosting
- Website Endpoint

The website files are stored in an Amazon S3 bucket configured for static website hosting. CloudFront can optionally be used to cache content closer to users around the world, improving performance.

---

# Implementation Steps

## Step 1: Create an Amazon S3 Bucket

1. Open the AWS Management Console.
2. Navigate to *Amazon S3*.
3. Click *Create bucket*.
4. Enter a globally unique bucket name.
5. Select your preferred AWS Region.
6. Disable *Block all public access*.
7. Acknowledge the warning.
8. Click *Create bucket*.

*Screenshot*

![S3 Bucket](screenshots/scenario2/scenario2-01-s3-bucket.png)

---

## Step 2: Upload Website Files

1. Open the newly created bucket.
2. Click *Upload*.
3. Upload the following files:
   - index.html
   - style.css
   - images (if available)
4. Complete the upload process.

*Screenshot*

![Uploaded Website Files](screenshots/scenario2/scenario2-02-uploaded-files.png)

---

## Step 3: Enable Static Website Hosting

1. Open the bucket.
2. Navigate to *Properties*.
3. Scroll to *Static Website Hosting*.
4. Click *Edit*.
5. Enable Static Website Hosting.
6. Specify:

- Index document:
```

index.html
```


7. Save the configuration.

*Screenshot*

![Static Website Hosting](screenshots/scenario2/scenario2-03-static-hosting.png)

---

## Step 4: Configure the Bucket Policy

To allow public access to the website, configure a bucket policy that grants read access to all users.

Example policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

Replace *YOUR-BUCKET-NAME* with the actual name of your S3 bucket.

*Screenshot*

![Bucket Policy](screenshots/scenario2/scenario2-04-bucket-policy.png)

---

## Step 5: Retrieve the Website Endpoint

After enabling static website hosting:

1. Open *Properties*.
2. Scroll to *Static Website Hosting*.
3. Copy the Website Endpoint URL.

This endpoint is used to access the hosted website.

*Screenshot*

![Website Endpoint](screenshots/scenario2/scenario2-05-website-endpoint.png)

---

## Performance Enhancement Using CloudFront

Amazon CloudFront can be integrated with the S3 bucket to:

- Reduce latency
- Cache content at edge locations
- Improve website performance
- Provide HTTPS support
- Enhance security

> *Note:* CloudFront is recommended for production environments but was optional in this implementation.

---

## Result

The static website was successfully deployed using Amazon S3. Website files are stored in the bucket, static website hosting is enabled, and the bucket is configured for public read access. This provides a simple, scalable, highly available, and cost-effective hosting solution.

---

## AWS Services Used

- Amazon S3
- Amazon CloudFront (Optional)
- IAM
- Bucket Policy
- Static Website Hosting

# Scenario 3: Hybrid Cloud Architecture for Legacy Applications

## Problem Statement

An organization currently operates a legacy application entirely within an on-premises data center. The infrastructure suffers from several limitations, including:

- Single points of failure
- Limited scalability
- Security vulnerabilities
- Inefficient resource utilization
- High infrastructure maintenance costs

The objective is to modernize the application by migrating it to AWS while retaining the existing on-premises database. The proposed solution should provide high availability, improved scalability, enhanced security, and reliable connectivity between AWS and the on-premises environment.

---

# Architecture Diagram

![Scenario 3 Architecture](diagrams/scenario3-05-architecture.png)

---

# Proposed Solution

The proposed hybrid cloud architecture consists of:

- Users accessing the application through the Internet.
- An Application Load Balancer distributing incoming traffic.
- Multiple Amazon EC2 instances deployed in different Availability Zones.
- An Auto Scaling Group for automatic scaling.
- Amazon VPC hosting the application servers.
- A Site-to-Site VPN providing secure communication.
- An on-premises firewall protecting internal resources.
- An on-premises database that stores application data.

This architecture provides a secure, scalable, and highly available environment while allowing the organization to continue using its existing database infrastructure.

---

# Implementation Steps

## Step 1: Create an Amazon VPC

1. Open the AWS Management Console.
2. Navigate to *VPC*.
3. Create a new VPC.
4. Configure the CIDR block.
5. Save the VPC.

*Screenshot*

![Amazon VPC](screenshots/scenario3/scenario3-01-vpc.png)

---

## Step 2: Create Public Subnets

1. Open the VPC Dashboard.
2. Navigate to *Subnets*.
3. Create public subnets in different Availability Zones.
4. Associate the subnets with the VPC.

*Screenshot*

![Public Subnets](screenshots/scenario3/scenario3-02-subnets.png)

---

## Step 3: Launch Amazon EC2 Instances

1. Open EC2 Dashboard.
2. Launch Ubuntu EC2 instances.
3. Place the instances inside the created VPC.
4. Attach the appropriate Security Group.

*Screenshot*

![Amazon EC2](screenshots/scenario3/scenario3-03-ec2-instance.png)

---

## Step 4: Configure Security Groups

Allow only the required inbound traffic.

Recommended rules:

| Protocol | Port | Purpose |
|----------|------|----------|
| SSH | 22 | Remote Administration |
| HTTP | 80 | Web Application |
| HTTPS | 443 | Secure Web Traffic |

*Screenshot*

![Security Group](screenshots/scenario3/scenario3-04-security-group.png)

---

## Step 5: Configure Secure Connectivity

A Site-to-Site VPN securely connects the AWS VPC to the on-premises data center.

Benefits include:

- Encrypted communication
- Secure database access
- Hybrid cloud connectivity
- Reliable communication

---

# High Availability

High availability is achieved through:

- Multiple EC2 instances
- Multiple Availability Zones
- Application Load Balancer
- Auto Scaling Group

This eliminates single points of failure and improves application resilience.

---

# Security Measures

The proposed architecture secures data by implementing:

- Security Groups
- Network isolation using Amazon VPC
- Site-to-Site VPN encryption
- HTTPS communication
- Least privilege IAM access
- Encrypted data in transit
- Encrypted data at rest

---

# Scalability

The application layer is designed for automatic scaling using an Auto Scaling Group.

Benefits include:

- Automatic scaling during high traffic
- Reduced infrastructure costs
- Improved application performance
- Better resource utilization

---

# Benefits of the Proposed Solution

The hybrid cloud architecture provides:

- High availability
- Improved scalability
- Enhanced security
- Cost optimization
- Reduced operational overhead
- Better disaster recovery capability
- Improved application performance
- Secure hybrid connectivity

---

# AWS Services Used

- Amazon EC2
- Amazon VPC
- Application Load Balancer
- Auto Scaling Group
- Security Groups
- Site-to-Site VPN
- Internet Gateway
- IAM

---

# Project Summary

This project demonstrated three practical AWS cloud solutions addressing common business challenges.

Scenario 1 implemented a multi-region architecture to reduce latency and improve user experience through regional deployment, Application Load Balancer, and CloudFront.

Scenario 2 demonstrated static website hosting using Amazon S3 with proper bucket configuration, permissions, and optional CloudFront integration.

Scenario 3 proposed a hybrid cloud architecture that modernizes legacy applications while maintaining an on-premises database through secure Site-to-Site VPN connectivity, high availability, and scalable cloud resources.

Together, these scenarios demonstrate the application of AWS best practices for performance, security, scalability, and reliability.

---

# Conclusion

This project demonstrated the practical application of AWS cloud services in solving three different real-world infrastructure challenges.

In *Scenario 1*, a multi-region architecture was designed to improve application performance and reduce latency for users located in India and London by leveraging Amazon EC2, Application Load Balancer, and Amazon CloudFront.

In *Scenario 2*, a static website was successfully hosted using Amazon S3. The solution highlighted how S3 can be configured for static website hosting, with appropriate permissions and optional CloudFront integration to improve performance and scalability.

In *Scenario 3*, a hybrid cloud architecture was proposed to modernize a legacy on-premises application. The solution addressed high availability, scalability, security, and resource optimization while maintaining the existing on-premises database through secure connectivity.

Overall, this project demonstrates how AWS cloud services can be combined to build secure, scalable, highly available, and cost-effective solutions that meet modern business requirements and industry best practices.

---

# References

1. [Amazon EC2 User Guide](https://docs.aws.amazon.com/ec2/)
2. [Amazon S3 User Guide](https://docs.aws.amazon.com/AmazonS3/)
3. [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/)
4. [Elastic Load Balancing User Guide](https://docs.aws.amazon.com/elasticloadbalancing/)
5. [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/)
6. [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/)
7. [AWS Architecture Center](https://aws.amazon.com/architecture/)
8. [AWS Documentation](https://docs.aws.amazon.com/)
9. [Amazon Route 53 Developer Guide](https://docs.aws.amazon.com/route53/)
10. [AWS Site-to-Site VPN User Guide](https://docs.aws.amazon.com/vpn/)

---

# Author

*Name:* Adepomola Ayomide

*Role:* Junior DevOps Engineer

*Project:* AWS Critical Thinking Project

*Platform:* Amazon Web Services (AWS)