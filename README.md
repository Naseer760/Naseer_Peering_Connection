# AWS VPC Peering — Cross-VPC Private Communication

**Project:** EWB AWS Internship  
**Student:** Kalavagadda Naseer  
**College:** Madanapalle Institute of Technology & Science  
**Batch:** AWS Internship Batch 1  
**Cloud Platform:** Amazon Web Services (AWS)  
**Region:** Asia Pacific (Mumbai) — `ap-south-1`  
**Service Category:** Networking & Security  
**Project Type:** Hands-on Cloud Infrastructure Project

---

## 1. Project Overview

This project demonstrates private network connectivity between two Amazon Virtual Private Clouds (VPCs) using AWS VPC Peering.

Two VPCs were configured with separate IPv4 CIDR blocks. EC2 instances, route tables, subnet associations, and security groups were used to establish and test communication between the private IP addresses of the workloads.

A bastion host in VPC A provides SSH access to the application instances. Connectivity was tested using Linux ping commands and SSH ProxyJump from Windows PowerShell.

The project follows the EWB Cloud Labs AWS VPC Peering Student Project Workbook.

## 2. Problem Statement

AWS resources deployed in separate VPCs are isolated by default. Applications hosted in different VPCs may need to communicate privately without relying on public IP addresses for communication between the networks.

The objective of this project was to establish a VPC peering connection, configure the required forward and return routes, apply appropriate security group rules, and validate private communication between EC2 instances.

## 3. Project Objectives

- Create and configure two VPCs with non-overlapping IPv4 CIDR ranges.
- Configure public and private subnets with explicit route table associations.
- Establish a VPC peering connection between the two VPCs.
- Configure route tables to direct traffic to the peer VPC.
- Configure security groups for the required SSH and ICMP traffic.
- Access private EC2 instances through a bastion host.
- Test forward and reverse connectivity using private IPv4 addresses.
- Document implementation evidence, troubleshooting, and test results.

## 4. Architecture and Configuration

### VPC A — `ewb-vpc-a`

- **VPC ID:** `vpc-0f04c9fb8933a150d`
- **VPC CIDR:** `10.10.0.0/16`
- **Public subnet:** `ewb-a-public`
- **Public subnet CIDR:** `10.10.1.0/24`
- **Public subnet ID:** `subnet-0e4d721a1e4fbe561`
- **Private subnet:** `ewb-a-private`
- **Private subnet CIDR:** `10.10.2.0/24`
- **Private subnet ID:** `subnet-0f748cc67c6e3c6f7`

### VPC B — `ewb-vpc-b`

- **VPC ID:** `vpc-099cace4d50b0400f`
- **VPC CIDR:** `10.20.0.0/16`
- **Private subnet:** `ewb-b-private`
- **Private subnet CIDR:** `10.20.1.0/24`
- **Private subnet ID:** `subnet-0f04d830bbbf8fac9`

### EC2 Instances

**Bastion Host**
- Instance ID: `i-0eac0ba8e4d719410`
- Initially reported public IPv4: `13.201.47.28`
- A later screenshot showed `13.201.0.96`; the current address should be verified in the EC2 console.

**App-A**
- Instance ID: `i-0bb30bd6ead4bf8a8`
- Earlier reported private IP: `10.10.1.155`
- A later terminal screenshot showed another host with private IP `10.10.1.52`.
- The instance ID, current private IP, and subnet association must be reconciled before final submission.

**App-B**
- Instance ID: `i-0aed1caf9796939a4`
- Private IP: `10.20.1.84`
- Operating system shown in terminal evidence: Amazon Linux 2023.

### VPC Peering Connection

- **Connection ID:** `pcx-075ba5989db37338f`
- **Reported status:** Active
- **Region:** `ap-south-1`

The AWS console screenshots show active routes to the peer VPC CIDRs. The peering connection provides the network link, while route tables and security groups control reachability.

## 5. Implementation Steps

### Step 1 — Configure the VPCs

Created two VPCs with distinct IPv4 CIDR ranges:

- VPC A: `10.10.0.0/16`
- VPC B: `10.20.0.0/16`

The CIDR ranges do not overlap, allowing the networks to be routed independently.

### Step 2 — Configure Subnets and EC2 Instances

Configured one public subnet and one private subnet in VPC A, along with a private subnet in VPC B.

The intended design uses a bastion in the public subnet and private workloads in the private subnets. The supplied screenshots show an App-A address discrepancy that must be resolved against the actual EC2 instance details.

### Step 3 — Create the VPC Peering Connection

Created the peering connection:

`pcx-075ba5989db37338f`

The connection was reported as Active. The route table screenshots also show the peering connection as the target of the peer-network routes.

### Step 4 — Configure Route Tables

The following route entries were visible in the supplied AWS console screenshots.

| Route table | Destination | Target | Status |
|---|---|---|---|
| `ewb-rt-a-public` | `0.0.0.0/0` | `igw-00b5c8c0a8b4625cb` | Active |
| `ewb-rt-a-public` | `10.10.0.0/16` | `local` | Active |
| `ewb-rt-a-public` | `10.20.0.0/16` | `pcx-075ba5989db37338f` | Active |
| `ewb-rt-a-private` | `10.10.0.0/16` | `local` | Active |
| `ewb-rt-a-private` | `10.20.0.0/16` | `pcx-075ba5989db37338f` | Active |
| `ewb-rt-b-private` | `10.10.0.0/16` | `pcx-075ba5989db37338f` | Active |
| `ewb-rt-b-private` | `10.20.0.0/16` | `local` | Active |

#### Route Table IDs

| Route table | ID |
|---|---|
| `ewb-rt-a-public` | `rtb-08b22623ec522319c` |
| `ewb-rt-a-private` | `rtb-0deafce15e791e90f` |
| `ewb-rt-b-private` | `rtb-0d09e1b8f2f14319c` |

### Step 5 — Configure Subnet Associations

The AWS console screenshots showed these explicit subnet associations.

| Route table        | Associated subnet | CIDR |
| `ewb-rt-a-public`  | `ewb-a-public`   | `10.10.1.0/24` |
| `ewb-rt-a-private` | `ewb-a-private`  |  `10.10.2.0/24` |
| `ewb-rt-b-private` | `ewb-b-private`  | `10.20.1.0/24` |

### Step 6 — Configure Security Groups

Security groups control which inbound traffic can reach the EC2 instances.

The EWB workbook calls for:
- SSH on TCP port 22 from approved sources.
- ICMP Echo Request rules for the peer workload's private IP.
- SSH access to App-A and App-B from the bastion's private IP.

The actual security group rule screenshots have not yet been supplied for this report. Therefore, the precise inbound rules and their current configuration remain to be verified.

SSH should not be unnecessarily exposed to `0.0.0.0/0`.

### Step 7 — Connect Using SSH

A Windows PowerShell terminal screenshot showed a successful TCP connection to the bastion on port 22.

The following SSH ProxyJump command was used to connect to App-B through the bastion:

```powershell
ssh -o ConnectTimeout=15 -i "$HOME\Downloads\ewb-lab.pem" -J ec2-user@13.201.47.28 ec2-user@10.20.1.84
```

A later screenshot showed the SSH configuration alias being used:

```bash
ssh ewb-app-b
```

The session connected successfully, and the command `hostname -I` returned `10.20.1.84`.

**Security note:** Never commit private SSH keys, AWS access keys, or credentials to GitHub.

## 6. Connectivity Testing and Results

The following results are based on the supplied terminal screenshots.

| Test | Observed result |
|---|---|
| TCP connectivity to the initially reported bastion IP on port 22 | Successful |
| SSH connection to the bastion | Successful |
| SSH access to App-B through the bastion | Successful |
| Forward ping from `10.10.1.155` to `10.20.1.84` | 4 packets received, 0% packet loss |
| Reverse ping from `10.20.1.84` to `10.10.1.155` | 4 packets received, 0% packet loss |
| Later ping from `10.10.1.52` to `10.20.1.0` | 100% packet loss |

The forward test showed an average round-trip time of approximately `0.206 ms`. The reverse test showed an average of approximately `0.162 ms`.

The failed ping targeted `10.20.1.0`, which is the subnet network address rather than the App-B host address `10.20.1.84`. It is therefore not evidence of the required controlled security-group failure.

### Important Validation Note

The successful forward and reverse tests demonstrate ICMP reachability between the tested addresses at the time of testing.

The App-A private IP and subnet association discrepancy must be reconciled before the final report is treated as complete.

## 7. Troubleshooting Considerations

If instances cannot communicate through VPC peering, check:

- The peering connection is Active.
- Both VPC CIDR ranges are non-overlapping.
- The relevant route tables include the correct peer CIDR and peering target.
- Route tables are associated with the intended subnets.
- Security groups allow the required source, protocol, and port.
- Network ACLs permit the necessary inbound and outbound traffic.
- The destination private IP belongs to the intended EC2 instance.
- SSH key paths, usernames, and ProxyJump configuration are correct.

A failed ping does not automatically mean that VPC peering is broken. ICMP may be blocked by a security group, network ACL, or host firewall, or the destination address may be incorrect.

## 8. Key Learning Outcomes

Through this project, I practised:

- AWS VPC architecture and IPv4 CIDR planning.
- Creating VPC peering connections.
- Configuring route tables and subnet associations.
- Understanding forward and return routing.
- Accessing private EC2 instances through a bastion host.
- Using SSH and ProxyJump.
- Testing private network connectivity using ICMP.
- Troubleshooting and documenting AWS networking resources.

## 9. Limitations and Future Improvements

This project was created as a hands-on learning environment and is not intended to represent a production-ready enterprise network.

The following items remain to be completed or verified:

- Reconcile App-A's actual instance ID, private IP, and subnet association.
- Verify the current bastion public IPv4 address.
- Capture and document the actual security group inbound rules.
- Complete the controlled ICMP failure-and-recovery test.
- Verify that both application instances have no public IPv4 addresses.
- Capture resource cleanup evidence after completing the project.

Future improvements could include AWS Systems Manager Session Manager, stricter security group rules, infrastructure as code, centralized logging, and automated connectivity testing.



## Conclusion

This project provided practical experience in connecting separate AWS VPCs through VPC peering. The route-table screenshots showed the required peer routes and subnet associations, and the supplied terminal evidence demonstrated successful forward and reverse ping tests and SSH access to App-B through the bastion.

The project strengthened my understanding of AWS networking, private IP communication, routing, security groups, SSH access, and connectivity troubleshooting. Final validation remains necessary for the App-A address discrepancy and the controlled ICMP failure-and-recovery test.
