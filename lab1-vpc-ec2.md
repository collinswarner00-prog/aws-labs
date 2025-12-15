# Lab 1: Public + Private EC2 in VPC
## Architecture Diagram
+-------------------------------------------------------------+
|                        VPC (10.0.0.0/16)                   |
|                                                             |
|   +----------------------+      +-------------------------+ |
|   | Public Subnet        |      | Private Subnet          | |
|   | (10.0.1.0/24)        |      | (10.0.2.0/24)           | |
|   |----------------------|      |-------------------------| |
|   | [Public Route Table] |      | [Private Route Table]   | |
|   | 0.0.0.0/0 → IGW      |      | 0.0.0.0/0 → S3 Endpoint| |
|   |                      |      |                         | |
|   | [Internet Gateway]   |      | [S3 VPC Endpoint]       | |
|   |        |             |      |        |                | |
|   | [Public EC2]         |      | [Private EC2]           | |
|   | - SSH from internet  |      | - SSH via Public EC2    | |
|   | - Web server (HTTP)  |      | - Access S3 bucket      | |
|   +----------------------+      +-------------------------+ |
|                                                             |
+-------------------------------------------------------------+
