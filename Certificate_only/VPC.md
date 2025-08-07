# Amazon VPC (Virtual Private Cloud) - Deep Dive

Amazon VPC lets you provision a logically isolated section of the AWS cloud where you can launch AWS resources in a virtual network that **you define**. It gives you full control over networking aspects like IP addressing, subnets, route tables, and gateways.

---

## 1. **Why VPC?**

* Isolation and security
* Define IP ranges using CIDR blocks
* Custom control over routing
* Control inbound/outbound traffic
* Use with services like EC2, RDS, Lambda (via ENIs), and more

---

## 2. **VPC Core Components**

### a. **CIDR Block**

* Private IP address range: `10.0.0.0/16`, `192.168.0.0/16`, etc.
* Min: `/28` (16 IPs), Max: `/16` (65,536 IPs)

### b. **Subnets**

* Sub-divide VPC CIDR block into smaller ranges
* **Public Subnet**: Has route to Internet Gateway
* **Private Subnet**: No direct internet access

### c. **Route Tables**

* Define how traffic is routed
* Associated with subnets

### d. **Internet Gateway (IGW)**

* Allows internet access to/from public subnets
* Must be attached to VPC and referenced in route table

### e. **NAT Gateway / NAT Instance**

* Allow private subnet instances to access the internet **without** receiving inbound traffic
* NAT Gateway = managed & scalable (preferred)
* Must be placed in a public subnet

### f. **Elastic IP**

* Static public IP associated with AWS resources (e.g., NAT Gateway, EC2)

---

## 3. **VPC Advanced Networking Components**

### a. **VPC Peering**

* Connect two VPCs privately
* **Transitive routing NOT allowed**
* Must update route tables manually

### b. **Transit Gateway**

* Hub-and-spoke model to connect multiple VPCs and on-prem networks
* Supports transitive routing

### c. **VPC Endpoints**

* **Interface Endpoints**: ENIs for private service access (e.g., S3, DynamoDB)
* **Gateway Endpoints**: Route traffic for S3/DynamoDB privately without internet access

### d. **Carrier Gateway**

* For AWS Outposts (connect to telecom networks)

### e. **Egress-Only Internet Gateway**

* For **IPv6** outbound-only traffic from private subnets

---

## 4. **Security in VPC**

### a. **Security Groups (SG)**

* Virtual firewall at **instance level**
* Stateful: return traffic is automatically allowed
* Allow rules only

### b. **Network ACLs (NACL)**

* Stateless: Rules must be defined for both directions
* At **subnet level**
* Allow and deny rules supported

### c. **Flow Logs**

* Capture IP traffic info to/from network interfaces
* Stored in CloudWatch or S3

---

## 5. **High Availability & Best Practices**

### a. **Subnets Across AZs**

* Design with multiple Availability Zones (AZs)
* Distribute resources for fault tolerance

### b. **Auto Scaling + Load Balancers**

* Place ALBs/NLBs in public subnet, backend in private subnets

### c. **Route 53 + Private Hosted Zones**

* Use for internal DNS resolution within VPC

---

## 6. **VPC Design Patterns**

| Design Pattern | Description                                                    |
| -------------- | -------------------------------------------------------------- |
| Hub-and-Spoke  | Use Transit Gateway as central hub                             |
| Flat VPC       | All services in a single VPC                                   |
| Shared VPC     | Centralized networking, separate accounts deploy into same VPC |

---

## 7. **Common VPC Limits**

* 5 VPCs per region (default; can be increased)
* 200 subnets per VPC
* 5000 route table entries
* 50 security groups per instance

---

## 8. **VPC Troubleshooting**

* Check route tables and subnet associations
* Confirm NACL/Security Group rules
* Ensure IGW/NAT is properly attached
* Use **Reachability Analyzer** for network diagnostics

---

## 9. **Exam Tips for AWS SAA**

* Know the difference between IGW and NAT
* Understand when to use Gateway vs Interface endpoints
* Remember SGs are stateful, NACLs are stateless
* Spot differences between VPC Peering vs Transit Gateway
* VPC Endpoints allow **private connectivity** to services like S3 without internet access


# VPC Deep Dive

## 1. Internet Gateway (IGW) vs NAT Gateway (NAT GW)

| Feature           | Internet Gateway (IGW)                     | NAT Gateway                                                       |
| ----------------- | ------------------------------------------ | ----------------------------------------------------------------- |
| Purpose           | Enables internet access for public subnets | Enables private subnets to access internet (outbound)             |
| Traffic Direction | Inbound + Outbound                         | Outbound only                                                     |
| IP Addressing     | Needs a public IP or Elastic IP            | Uses Elastic IP                                                   |
| Placement         | Attached to the VPC                        | Deployed in a public subnet                                       |
| Use Case          | Public-facing services (e.g., web servers) | Private services needing internet access (e.g., software updates) |

**When to use:**

* Use **IGW** when you want **inbound/outbound** internet traffic.
* Use **NAT GW** to keep services private but still allow **outbound** internet access (e.g., to download patches).

---

## 2. Gateway vs Interface Endpoints

| Feature      | Gateway Endpoint               | Interface Endpoint                          |
| ------------ | ------------------------------ | ------------------------------------------- |
| Connectivity | S3, DynamoDB (via route table) | Other AWS services (via ENI in your subnet) |
| Access Type  | Gateway in route tables        | ENI with private IPs                        |
| Cost         | Free                           | Charged per hour + data processed           |
| Security     | Uses route table entries       | ENI supports SGs, VPC Flow Logs             |

**When to use:**

* Use **Gateway endpoints** for S3/DynamoDB to avoid NAT/internet access.
* Use **Interface endpoints** for services like EC2 API, CloudWatch, Secrets Manager, etc., when private connectivity is needed with granular security.

---

## 3. Security Groups (SGs) vs Network ACLs (NACLs)

| Feature          | Security Group        | NACL                               |
| ---------------- | --------------------- | ---------------------------------- |
| Stateful         | Yes                   | No (Stateless)                     |
| Applied To       | ENIs (instance-level) | Subnets                            |
| Rules            | Allow rules only      | Allow and Deny rules               |
| Evaluation Order | Evaluated as a whole  | Rule order matters (lowest # wins) |
| Logging          | VPC Flow Logs         | VPC Flow Logs (for subnet traffic) |

**Key Concepts:**

* **Stateful SGs**: If you allow inbound, response is automatically allowed.
* **Stateless NACLs**: You must allow both inbound and outbound separately.

**Use Cases:**

* SGs: Use to control traffic to/from EC2 instances.
* NACLs: Use as subnet-level firewalls for coarse-grained filtering.

---

## 4. VPC Peering vs Transit Gateway (TGW)

| Feature            | VPC Peering                     | Transit Gateway                      |
| ------------------ | ------------------------------- | ------------------------------------ |
| Connectivity       | One-to-one (mesh grows rapidly) | One-to-many hub model                |
| Transitive Routing | Not supported                   | Supported                            |
| Complexity         | Higher with many VPCs           | Simplified hub-and-spoke             |
| Cost               | No hourly cost, data charges    | Charged per attachment/hour and data |

**When to use:**

* Use **VPC peering** for **few VPCs** with direct/simple needs.
* Use **Transit Gateway** when you need **centralized routing** and **scalable multi-VPC/multi-account** architecture.

---

## 5. AWS PrivateLink & VPC Endpoints

**PrivateLink (Interface VPC Endpoints):**

* Create a private ENI to connect to AWS services or third-party services **privately** within your VPC.
* Avoids public internet, NAT, or IGW.
* Supports services like S3, EC2 API, KMS, Secrets Manager, CloudWatch, etc.
* More secure, reduces data exposure.

**Use Case:**

* **PrivateLink peering** enables secure private connections between VPCs or services **without routing traffic through the public internet** or even VPC peering.

---

## 6. VPC Endpoints for Private Connectivity

* **Gateway endpoints** (S3, DynamoDB) route traffic internally using the route table.
* **Interface endpoints** (PrivateLink) create ENIs in subnets to communicate privately.
* **Benefits:**

  * No need for IGW/NAT
  * Improved security
  * Reduced latency
  * Helps meet compliance requirements

**Example:**
Access S3 privately without using public IPs or going through the internet.

