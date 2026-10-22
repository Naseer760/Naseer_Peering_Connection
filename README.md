# AWS VPC Peering — Private Network Connectivity

## Project Overview

This project demonstrates how to establish private communication between two Amazon Virtual Private Clouds (VPCs) using **AWS VPC Peering**. It uses EC2 instances, public and private subnets, route tables, security groups, and a bastion host for secure administration.

The project follows the EWB Cloud Labs AWS VPC Peering Student Project Workbook.

## Architecture

- **VPC A:** `10.10.0.0/16`
- **VPC B:** `10.20.0.0/16`
- **Public Subnet A:** `10.10.1.0/24`
- **Private Subnet A:** `10.10.2.0/24`
- **Private Subnet B:** `10.20.1.0/24`
- **AWS Region:** `ap-south-1` (Mumbai)
- **Compute:** Amazon EC2 with Amazon Linux 2023

## Technologies Used

- Amazon Web Services (AWS)
- Amazon VPC and VPC Peering
- Amazon EC2
- Route Tables and Subnet Associations
- Security Groups
- Internet Gateway
- SSH and ProxyJump
- Linux networking commands

## Key Features

- Establishes a peering connection between two VPCs.
- Configures forward and return routes.
- Uses a bastion host to access private EC2 instances.
- Restricts inbound traffic using security group rules.
- Tests connectivity using ICMP ping.
- Documents route configurations and troubleshooting evidence.

## Testing Results

- **Forward connectivity:** App-A to App-B — 4 packets received, 0% packet loss.
- **Reverse connectivity:** App-B to App-A — 4 packets received, 0% packet loss.
- **Private SSH access:** Successfully connected to App-B through the bastion host.
- **Controlled security-group failure and recovery:** Pending completion.

## Learning Outcomes

- Understanding VPC CIDR planning and subnet design.
- Configuring AWS route tables and VPC peering.
- Understanding forward and return network routes.
- Managing secure SSH access through a bastion host.
- Troubleshooting private network connectivity.

## Project Status

Core routing and connectivity tests have been demonstrated. The App-A address/subnet discrepancy and controlled ICMP failure-and-recovery test remain to be resolved.

## Author

**Kalavagadda Naseer**    
Madanapalle Institute of Technology & Science


