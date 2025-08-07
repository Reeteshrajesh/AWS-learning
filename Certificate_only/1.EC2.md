# Amazon EC2 (Elastic Compute Cloud) – Deep Dive Guide

## 📘 Overview

Amazon EC2 (Elastic Compute Cloud) provides scalable compute capacity in the AWS cloud. It allows you to run virtual servers (instances) for various use cases such as web hosting, backend services, and development environments.

---

## 🧠 Key Concepts

### 1. **EC2 Instances**

* Virtual servers in the cloud.
* You choose an AMI (Amazon Machine Image), instance type, storage, and networking options.

### 2. **AMI (Amazon Machine Image)**

* Template for the root volume of your instance (OS + pre-installed software).
* Types:

  * AWS-provided (e.g., Amazon Linux, Ubuntu)
  * Marketplace AMIs
  * Custom AMIs (created by users)

### 3. **Instance Types**

* Define CPU, memory, storage, and networking capacity.
* Families:

  * **General Purpose**: t3, t4g, m6i
  * **Compute Optimized**: c7g, c6i
  * **Memory Optimized**: r6i, x2idn
  * **Storage Optimized**: i4i, d3
  * **Accelerated Computing**: p4, inf2

### 4. **Instance Lifecycle States**

* Pending
* Running
* Stopping / Stopped
* Shutting-down
* Terminated

---

## 💵 Pricing Models

### 1. **On-Demand Instances**

* Pay by the second.
* Flexible, no commitment.

### 2. **Reserved Instances (RIs)**

* 1 or 3 year commitment.
* Save up to 72%.
* Options: Standard, Convertible

### 3. **Spot Instances**

* Up to 90% discount.
* Interruption possible with 2-minute notice.

### 4. **Savings Plans**

* Commit to a dollar amount/hour over 1 or 3 years.
* Flexible across instance types and regions.

### 5. **Dedicated Hosts / Instances**

* Physically isolated hardware.
* Useful for licensing or compliance requirements.

---

## 🛡️ Security

### 1. **Key Pairs**

* SSH access to Linux.
* RDP access to Windows.
* Private key stays with you.

### 2. **Security Groups**

* Virtual firewall at instance level.
* Controls inbound and outbound traffic.

### 3. **NACLs (Network ACLs)**

* Optional, stateless layer of security at subnet level.

---

## 🏗️ Launch Process (Step-by-Step)

1. Choose AMI
2. Choose Instance Type
3. Configure Instance (network, IAM role, etc.)
4. Add Storage (EBS, Instance Store)
5. Add Tags (Name = WebServer)
6. Configure Security Group
7. Review and Launch

---

## 💾 EC2 Storage Options

### 1. **EBS (Elastic Block Store)**

* Network-attached, durable.
* Volume types:

  * gp3: General purpose
  * io2/io2 Block Express: High IOPS
  * st1/sc1: HDD, throughput optimized
* Snapshots support backups.

### 2. **Instance Store**

* Ephemeral, physically attached.
* Data lost on stop/terminate.

### 3. **EFS (Elastic File System)**

* Shared NFS file system across instances.

### 4. **FSx**

* Fully managed file systems (e.g., Windows, Lustre).

---

## 🌐 EC2 Networking

### 1. **VPC Integration**

* Each EC2 instance is launched into a VPC subnet.

### 2. **Elastic IPs**

* Static public IPv4 address.

### 3. **ENI (Elastic Network Interface)**

* Virtual NIC attached to an instance.

### 4. **Placement Groups**

* Control placement across AZs or within a single AZ:

  * Cluster
  * Spread
  * Partition

### 5. **Elastic Load Balancer (ELB)**

* Distribute traffic to EC2 instances.

---

## 🔄 EC2 Auto Scaling

* Automatically adjust number of EC2 instances.
* Scaling policies: Target Tracking, Step Scaling, Scheduled
* Launch Templates vs Launch Configurations

---

## 🧰 Additional Features

### 1. **User Data Scripts**

* Run code at launch (e.g., install software).
* Example (Linux):

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
```

### 2. **CloudWatch Monitoring**

* Basic and Detailed Monitoring
* Metrics: CPU, Disk, Network

### 3. **CloudTrail Logging**

* Track API calls to and from EC2.

### 4. **EC2 Image Builder**

* Automate AMI creation pipelines.

---

## 📝 Exam Tips

* Understand the difference between EBS and Instance Store.
* Know when to use Spot vs On-Demand vs RIs.
* Study security group vs NACL behavior.
* Practice setting up Auto Scaling and Load Balancer.
* Learn the IAM Role attachment process for EC2.
* Review EC2 launch troubleshooting (e.g., key pair, networking).

---

## ✅ Hands-on Practice Ideas

* Launch EC2 instance using Amazon Linux AMI.
* Attach EBS volume and mount it.
* Create a custom AMI.
* Use user-data to install Apache.
* Setup ELB + Auto Scaling group with EC2 backend.


### 1. **Amazon EBS (Elastic Block Store)**

**What is EBS?**
Amazon EBS provides durable, block-level storage volumes for use with EC2 instances.

**Types of EBS Volumes:**

* **gp3**: General Purpose SSD (recommended default)
* **gp2**: Older version of general SSD
* **io2/io1**: Provisioned IOPS SSD – high performance, mission-critical
* **st1**: Throughput-optimized HDD – big data, log processing
* **sc1**: Cold HDD – infrequent access

**Key Features:**

* Can be attached/detached to/from EC2
* Supports snapshots to S3
* Encryption at rest with AWS KMS
* Resizable volumes

---

### 2. **EBS vs Instance Store**

| Feature     | EBS                      | Instance Store              |
| ----------- | ------------------------ | --------------------------- |
| Durability  | Persistent               | Ephemeral (lost on stop)    |
| Backup      | Snapshots supported      | No backup capability        |
| Use case    | Critical data, databases | Temp storage, buffers       |
| Performance | Consistent (SSD options) | High throughput/low latency |
| Lifecycle   | Independent of EC2       | Tied to EC2 lifecycle       |

**When to use Instance Store:**

* Caching
* Buffer/temp files
* High-performance apps where durability isn't needed

---

### 3. **Spot vs On-Demand vs Reserved Instances (RIs)**

| Model     | Description                               | Use Case                                  |
| --------- | ----------------------------------------- | ----------------------------------------- |
| On-Demand | Pay per second with no commitment         | Short-term, spiky workloads               |
| Reserved  | 1 or 3-year commitment, huge discount     | Steady-state, predictable workloads       |
| Spot      | Bid for spare capacity, up to 90% cheaper | Fault-tolerant, flexible jobs (batch, ML) |

**Note:** Spot instances can be interrupted with 2 minutes warning.

---

### 4. **Security Groups vs NACLs**

| Feature    | Security Group       | Network ACL                  |
| ---------- | -------------------- | ---------------------------- |
| Scope      | Instance level       | Subnet level                 |
| Stateful?  | Yes                  | No                           |
| Rules      | Only allow rules     | Allow and deny               |
| Evaluation | All rules evaluated  | Rules evaluated in order     |
| Default    | All outbound allowed | All inbound/outbound allowed |

**Best Practice:**

* Use SGs for instance-specific traffic
* Use NACLs for subnet-wide access control

---

### 5. **IAM Role Attachment Process for EC2**

**Purpose:** Allows EC2 to access AWS services without credentials stored on instance.

**Steps:**

1. Create an IAM role with policies (e.g., S3 access)
2. Select **"EC2"** as the trusted entity
3. Attach role during EC2 launch or after (via console/CLI)

**Benefits:**

* No hardcoded credentials
* Temporary, auto-rotated credentials

**Attach After Launch (CLI Example):**

```bash
aws ec2 associate-iam-instance-profile \
  --instance-id i-1234567890abcdef0 \
  --iam-instance-profile Name=MyEC2Role
```

---

### 6. **EC2 Launch Troubleshooting**

**Common Issues:**

* **No SSH Access:** Check security group (port 22), NACL, key pair
* **Incorrect AMI/instance type**: Unsupported architecture
* **No internet access**: Missing public IP, NAT gateway, or IGW
* **Disk size wrong**: Resize using EBS modification

**Tips:**

* Always check VPC, subnet, route table, and IGW/NAT setup
* Use EC2 Instance Connect (browser SSH)

---

### 7. **Load Balancer + Auto Scaling Group (ASG) with EC2**

**Components:**

* **Launch Template**: Defines EC2 config (AMI, type, key pair)
* **Auto Scaling Group**: Manages EC2 scale-in/out
* **Elastic Load Balancer (ALB/NLB)**: Distributes traffic

**Flow:**

1. User hits Load Balancer
2. Load Balancer sends request to healthy EC2 (via ASG)
3. ASG adds/removes EC2s based on metrics (CPU, requests)

**Benefits:**

* High availability
* Fault tolerance
* Automated scaling

**Best Practices:**

* Use ALB for HTTP(S)
* Define health checks
* Use launch templates over legacy launch configurations
