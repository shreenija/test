# VPC Creation

## Step 1: Create the VPC

Navigate to:

```text
AWS Console → VPC → Create VPC
```

Select **"VPC and More"** and provide a name for the VPC.

![Create VPC](images/vpc-create.png)

Specify the **IPv4 CIDR Block** for the VPC. The CIDR block defines the range of private IP addresses available within the VPC.

Example:

```text
10.0.0.0/16
```

![VPC CIDR Configuration](images/vpc-cidr.png)

---

## Step 2: Configure Network Resources

Configure the networking components as shown below:

| Parameter | Value |
|------------|---------|
| Availability Zones | 2 or more for High Availability |
| Public Subnets | 1 per Availability Zone |
| Private Subnets | 1 per Availability Zone |
| NAT Gateway | 1 per Availability Zone |

![Network Configuration](images/network-configuration.png)

---

## Subnet Usage

### Public Subnets

Public subnets host internet-facing resources such as:

- Application Load Balancers (ALB)
- Bastion Hosts
- NAT Gateways

### Private Subnets

Private subnets host backend resources that should not be directly accessible from the internet, such as:

- Application Servers
- Database Servers
- Internal Services

![Public and Private Subnets](images/public-private-subnets.png)

---

## Step 3: Configure NAT Gateway

Create a NAT Gateway for each Availability Zone.

![NAT Gateway Configuration](images/nat-gateway.png)

### Purpose of NAT Gateway

The NAT Gateway enables instances in private subnets to access the internet without exposing them to inbound internet traffic.

Common use cases include:

- Downloading software packages
- Installing application dependencies
- Accessing external APIs
- Pulling container images
- Performing operating system updates

---

## Step 4: Create the VPC

Review all configurations and click **Create VPC**.

AWS will provision the following resources automatically:

- VPC
- Public Subnets
- Private Subnets
- Internet Gateway
- NAT Gateways
- Route Tables

![VPC Creation Complete](images/vpc-created.png)

---

## Step 5: Verify VPC Architecture

After the VPC is successfully created, open the **Resource Map** to view the generated architecture.

```text
VPC → Your VPC → Resource Map
```

The Resource Map provides a graphical representation of:

- VPC
- Subnets
- Route Tables
- Internet Gateway
- NAT Gateways

![Resource Map](images/resource-map.png)

---

## Route Table Verification

### Public Route Tables

Verify that each public subnet is associated with a Public Route Table containing a route to the Internet Gateway.

Example Route:

| Destination | Target |
|-------------|---------|
| 0.0.0.0/0 | Internet Gateway (IGW) |

This allows resources in public subnets to communicate directly with the internet.

![Public Route Table](images/public-route-table.png)

---

### Private Route Tables

Verify that each private subnet is associated with a Private Route Table containing a route to its respective NAT Gateway.

Example Route:

| Destination | Target |
|-------------|---------|
| 0.0.0.0/0 | NAT Gateway |

This configuration enables outbound internet access while preventing direct inbound internet connectivity.

![Private Route Table](images/private-route-table.png)

---

## Final Architecture Validation

Ensure the architecture meets the intended design:

- Public Subnets connected to the Internet Gateway
- NAT Gateways deployed in Public Subnets
- Private Subnets using NAT Gateways for outbound internet access
- Application resources isolated within Private Subnets
- High Availability achieved across multiple Availability Zones

### Expected Architecture Flow

```text
Internet
   │
   ▼
Internet Gateway
   │
   ▼
Public Subnets
   │
   ├── Application Load Balancer
   └── NAT Gateway
            │
            ▼
      Private Subnets
            │
            ▼
       EC2 Instances
```

![Final VPC Architecture](images/final-architecture.png)