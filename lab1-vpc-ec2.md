# Lab 1: Public + Private EC2 in VPC

## Architecture Diagram

```
+-------------------------------------------------------------+
|                      VPC (10.0.0.0/16)                      |
|                                                             |
|  +----------------------+     +---------------------------+ |
|  | Public Subnet        |     | Private Subnet            | |
|  | (10.0.1.0/24)        |     | (10.0.2.0/24)             | |
|  |----------------------|     |---------------------------| |
|  | [Public Route Table] |     | [Private Route Table]     | |
|  | 0.0.0.0/0 → IGW      |     | 0.0.0.0/0 → S3 Endpoint   | |
|  |                      |     |                           | |
|  | [Internet Gateway]   |     | [S3 VPC Endpoint]         | |
|  |        |             |     |        |                  | |
|  | [Public EC2]         |     | [Private EC2]             | |
|  | - SSH from internet  |     | - SSH via Public EC2      | |
|  | - Web server (HTTP)  |     | - Access S3 bucket        | |
|  +----------------------+     +---------------------------+ |
|                                                             |
+-------------------------------------------------------------+
```

---

## Steps Taken

### VPC and Networking Setup
1. Created a new VPC with CIDR block `10.0.0.0/16`.
2. Created two subnets:
   - Public Subnet: `10.0.1.0/24`
   - Private Subnet: `10.0.2.0/24`
3. Created two route tables:
   - Public Route Table → `0.0.0.0/0` → Internet Gateway
   - Private Route Table → `0.0.0.0/0` → S3 VPC Endpoint
4. Created an Internet Gateway (IGW) and attached it to the VPC.
5. Associated the Public Route Table with the Public Subnet.
6. Associated the Private Route Table with the Private Subnet.

### EC2 Setup
7. Created a key pair (`Lab1-KeyPair.pem`) for SSH access.
8. Launched a Public EC2 instance in the public subnet:
   - Auto-assign public IP enabled.
   - Security group allowed SSH (22) from my IP.
9. Launched a Private EC2 instance in the private subnet:
   - No public IP.
   - Security group allowed SSH (22) only from the Public EC2’s SG.

### S3 Connectivity
10. Created an S3 bucket (`lab1-test-bucket`).
11. Uploaded a test image (`test-image.png`) to the bucket.
12. Created an **S3 VPC Endpoint** and associated it with the Private Route Table.
13. Attached an IAM role to the Private EC2 with S3 read permissions.
14. Verified that the Private EC2 could access the S3 bucket:
    - Listed bucket contents with `aws s3 ls`.
    - Downloaded the test image with `aws s3 cp`.

### PuTTY Setup and SSH Verification (Windows)
15. Installed PuTTY and PuTTYgen.
16. Converted PEM key to PPK using PuTTYgen.
17. Configured PuTTY session for Public EC2:
    - Host Name: `ec2-user@<PublicIP>`
    - SSH → Auth → Private key file: `Lab1-KeyPair.ppk`
18. Connected to Public EC2 via PuTTY.
19. From Public EC2, SSH’d into Private EC2 using its private IP.

### Connectivity Tests and Optional Web Server
20. Verified connectivity:
    - SSH into Public EC2 from PuTTY.
    - SSH into Private EC2 via Public EC2.
    - Tested outbound connectivity with `ping google.com` and `curl https://aws.amazon.com`.
21. (Optional) Installed Apache web server on Public EC2:
    - Updated SG to allow HTTP (80) from anywhere.
    - Created a simple HTML page and verified it in browser.

---

## Commands Used

### Connecting to EC2
1. `ssh -i Lab1-KeyPair.pem ec2-user@<PublicIP>`  
   - Connect securely to the Public EC2 instance from your local machine.

2. `ssh ec2-user@10.0.2.x`  
   - From the Public EC2, connect to the Private EC2 using its private IP.

---

### Testing Connectivity
3. `ping google.com`  
   - Sends ICMP packets to Google to verify outbound internet connectivity.

4. `curl https://aws.amazon.com`  
   - Fetches a webpage to confirm HTTP/HTTPS connectivity from the Private EC2.

---

### S3 Bucket Access
5. `aws s3 ls s3://lab1-test-bucket`  
   - Lists all objects stored in the S3 bucket.

6. `aws s3 cp s3://lab1-test-bucket/test-image.png .`  
   - Downloads the test image from S3 to the Private EC2 instance.

---

### Optional Web Server Setup
7. `sudo yum update -y`  
   - Updates all installed packages to the latest version.

8. `sudo yum install -y httpd`  
   - Installs Apache HTTP server.

9. `sudo systemctl start httpd`  
   - Starts the Apache service.

10. `sudo systemctl enable httpd`  
    - Ensures Apache starts automatically on reboot.

11. `echo "<h1>Hello from Warner's EC2</h1>" | sudo tee /var/www/html/index.html`  
    - Creates a simple HTML page in the Apache web root so it can be viewed in a browser.


