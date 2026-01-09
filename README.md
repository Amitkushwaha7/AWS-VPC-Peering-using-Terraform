# AWS Multi-Region VPC Peering with Terraform

[![Terraform](https://img.shields.io/badge/Terraform-%3E%3D1.6-623CE4?logo=terraform)](https://www.terraform.io/)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazon-aws)](https://aws.amazon.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📖 Overview

This project demonstrates **AWS VPC Peering** across three different AWS regions using Terraform. It creates a **full mesh topology** where all VPCs can communicate directly with each other using private IP addresses.

### Architecture

![AWS VPC Peering Architecture](Assets/00-architecture-diagram.png)
*Multi-Region VPC Peering Architecture - Full Mesh Topology*

### Key Features

- 🌍 **Multi-Region**: 3 VPCs across 3 AWS regions (us-east-1, us-west-2, eu-west-2)
- 🔗 **Full Mesh Peering**: All VPCs directly connected (3 peering connections)
- 🚀 **Automated Deployment**: Complete Infrastructure as Code with Terraform
- 🔒 **Secure**: Security groups configured for cross-VPC communication
- 📊 **Production-Ready**: Proper tagging, dependencies, and validation

### What Gets Created

| Resource Type | Count | Description |
|--------------|-------|-------------|
| VPCs | 3 | One in each region (10.0.0.0/16, 10.1.0.0/16, 10.2.0.0/16) |
| Subnets | 3 | Public subnet in each VPC |
| Internet Gateways | 3 | One per VPC for internet access |
| Route Tables | 3 | With peering and internet routes |
| VPC Peering Connections | 3 | Full mesh topology |
| Security Groups | 3 | SSH, ICMP, and TCP rules |
| EC2 Instances | 3 | Ubuntu 24.04 + Apache web server |

**Total Resources**: ~35 AWS resources

---

## 🚀 Quick Start

### Prerequisites

- AWS Account with appropriate permissions
- AWS CLI configured
- Terraform >= 1.6.0
- SSH key pairs in all three regions

### Deployment Steps

1. **Clone and Navigate**
   ```bash
   cd terraform
   ```

2. **Configure Variables**
   ```bash
   cp terraform.tfvars.example terraform.tfvars
   # Edit terraform.tfvars with your SSH key names
   ```

3. **Deploy Infrastructure**
   ```bash
   terraform init
   terraform plan
   terraform apply
   ```

4. **Test Connectivity**
   ```bash
   # Get instance IPs
   terraform output
   
   # SSH into any instance and ping others using private IPs
   ssh -i your-key.pem ubuntu@<PUBLIC_IP>
   ping <PRIVATE_IP_OF_OTHER_VPC>
   ```

5. **Cleanup**
   ```bash
   terraform destroy
   ```

---

## 📚 Documentation

- **[Demo.md](Demo.md)** - Complete implementation guide with detailed explanations
- **[Terraform Files](terraform/)** - Infrastructure as Code

---

## 🏗️ Architecture Details

### Network Design

```
Primary VPC (us-east-1)          Tertiary VPC (eu-west-2)          Secondary VPC (us-west-2)
10.0.0.0/16                      10.2.0.0/16                       10.1.0.0/16
     │                                 │                                 │
     ├─ Subnet: 10.0.1.0/24           ├─ Subnet: 10.2.1.0/24           ├─ Subnet: 10.1.1.0/24
     ├─ Internet Gateway              ├─ Internet Gateway              ├─ Internet Gateway
     ├─ EC2 Instance                  ├─ EC2 Instance                  ├─ EC2 Instance
     │                                 │                                 │
     └────────────VPC Peering──────────┴────────────VPC Peering─────────┘
```

### VPC Peering Connections (Full Mesh)

1. **Primary ↔ Secondary** (us-east-1 ↔ us-west-2)
2. **Primary ↔ Tertiary** (us-east-1 ↔ eu-west-2)
3. **Secondary ↔ Tertiary** (us-west-2 ↔ eu-west-2)

---

## 💡 Key Concepts

- **VPC Peering**: Network connection between two VPCs for private communication
- **Full Mesh Topology**: All VPCs directly connected (vs hub-and-spoke)
- **Cross-Region Peering**: Peering connections spanning multiple AWS regions
- **Non-Overlapping CIDRs**: Required for VPC peering to work

---

## ⚠️ Important Notes

### Costs
This project creates billable AWS resources:
- 3x t2.micro EC2 instances
- Cross-region data transfer
- VPC peering data transfer

**Estimated cost**: ~$15-25/month if left running

**Always destroy resources when done:**
```bash
terraform destroy
```

### Limitations
- VPC peering is **not transitive**
- CIDR blocks **must not overlap**
- Maximum 125 peering connections per VPC

---

## 🛠️ Project Structure

```
.
├── Assets/                    # Architecture diagrams and screenshots
├── Demo.md                    # Detailed implementation guide
├── Readme.md                  # This file
└── terraform/
    ├── backend.tf            # S3 backend configuration
    ├── data.tf               # Data sources (AMIs, AZs)
    ├── locals.tf             # User data scripts
    ├── main.tf               # Main infrastructure
    ├── outputs.tf            # Output values
    ├── providers.tf          # Multi-region providers
    ├── variables.tf          # Variable definitions
    └── terraform.tfvars.example
```

---

## 📖 Learning Outcomes

After completing this project, you'll understand:

✅ How to create cross-region VPC peering connections  
✅ How to configure routing for VPC peering  
✅ How to set up security groups for cross-VPC communication  
✅ How to use Terraform provider aliases for multi-region deployments  
✅ How to test and verify VPC peering connectivity  
✅ VPC peering limitations and best practices  

---

## 🔗 Resources

- [AWS VPC Peering Documentation](https://docs.aws.amazon.com/vpc/latest/peering/)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Complete Implementation Guide](Demo.md)

---

## 📝 License

This project is licensed under the MIT License.

---

**Made with ❤️ using Terraform and AWS**
