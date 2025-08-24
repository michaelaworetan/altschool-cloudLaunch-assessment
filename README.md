# ☁️ CloudLaunch AWS Deployment README 💻

### This project details the deployment of a lightweight, secure product named **CloudLaunch** on AWS. The solution leverages core services like **S3** for static website hosting and file storage, **IAM** for robust access control, and **VPC** for a secure network foundation.

---

### ➡️ Task 1: Static Website Hosting & IAM Setup

### **Description of Steps**

1. **S3 Buckets:** Three S3 buckets were created:
    - `cloudlaunch-sitepub-michaelaworetan`: Configured for static website hosting with a public read-only bucket policy.
    - `cloudlaunch-siteprivate-michaelaworetan`: Created to store internal private documents. Public access was blocked.
    - `cloudlaunch-sitevol-michaelaworetan`: Created to be visible but with its contents inaccessible to the designated IAM user. Public access was blocked.
2. **Static Website:** The provided HTML, CSS, and JS files were uploaded to the `cloudlaunch-sitepub-michaelaworetan` bucket, and static website hosting was enabled in the bucket properties.
3. **IAM User & Policy:** An IAM user named `cloudlaunch-user` was created. A custom policy was attached to this user to enforce strict access controls on the S3 buckets and provide read-only access to VPC components. The policy explicitly denies `DeleteObject` permissions on all buckets.
4. **CloudFront (Bonus):** A CloudFront distribution was set up in front of the [https://dze8vneqs3jx1.cloudfront.net](dze8vneqs3jx1.cloudfront.net) to provide **HTTPS** and **global caching**, improving website security and performance.

---

### ➡️ Task 2: VPC Network Design

### **Description of Steps**

1. **VPC:** A new VPC, `cloudlaunch-vpc`, was created with a CIDR block of `10.0.0.0/16`.
2. **Subnets:** Three subnets were provisioned within the VPC:
    - `cloudlaunch-public-subnet` (`10.0.1.0/24`) for public-facing resources.
    - `cloudlaunch-app-subnet` (`10.0.2.0/24`) for private application servers.
    - `cloudlaunch-db-subnet` (`10.0.3.0/28`) for private database services.
3. **Internet Gateway:** An Internet Gateway named `cloudlaunch-igw` was created and attached to the VPC.
4. **Route Tables:**
    - `cloudlaunch-public-rt`: Configured to route internet-bound traffic (`0.0.0.0/0`) to the `cloudlaunch-igw` and associated with the public subnet.
    - `cloudlaunch-app-rt` and `cloudlaunch-db-rt`: These were left as private route tables with no route to the Internet Gateway, ensuring they are isolated from the internet.
5. **Security Groups:** Two security groups were created to control network traffic:
    - `cloudlaunch-app-sg`: Allows inbound **HTTP** (port 80) traffic only from within the VPC's CIDR block.
    - `cloudlaunch-db-sg`: Allows inbound **MySQL** (port 3306) traffic only from the `cloudlaunch-app-sg`.

---

### ➡️ Submission Details

### **CloudFront URL**

`https://cloudlaunch-sitepub-michaelaworetan.s3.eu-west-1.amazonaws.com/index.html` (Example URL, replace with your distribution's domain name)

### **IAM Policy for `cloudlaunch-user`**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::cloudlaunch-siteprivate-michaelaworetan/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch-sitepub-michaelaworetan/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::cloudlaunch-sitepub-michaelaworetan",
                "arn:aws:s3:::cloudlaunch-siteprivate-michaelaworetan",
                "arn:aws:s3:::cloudlaunch-sitevol-michaelaworetan"
            ]
        },
        {
            "Effect": "Deny",
            "Action": "s3:DeleteObject",
            "Resource": [
                "arn:aws:s3:::cloudlaunch-sitepub-michaelaworetan/*",
                "arn:aws:s3:::cloudlaunch-siteprivate-michaelaworetan/*",
                "arn:aws:s3:::cloudlaunch-sitevol-michaelaworetan/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeSubnets",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        }
    ]
}

```

### **Account Information(details submitted in the form)**

- **Console URL:**
- **Account ID:**
- **IAM User:**
- **Initial Password:**  (This password will require a change upon first login)
