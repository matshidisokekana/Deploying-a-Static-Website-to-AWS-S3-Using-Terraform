# Deploying-a-Static-Website-to-AWS-S3-Using-Terraform
Absolutely! Here's a **complete README.md** draft you can copy and post directly to your GitHub project, fully following the structure and style of the Medium article you shared (but without GitHub Actions).
It includes a clear introduction, setup explanation, code breakdown, and deployment steps — ready for your GitHub 💥

---

# 🚀 Deploying a Static Website to AWS S3 Using Terraform

## 📝 Introduction

In this project, we deploy a static website to an **Amazon S3 bucket** using **Terraform**.
A static website allows you to host HTML, CSS, JavaScript, and other static files directly in S3 without the need for a backend server.

Terraform helps us define and provision our S3 bucket and related resources as code (Infrastructure as Code), making it easy to manage and replicate.

---

## 🌟 Project Overview

This project covers:

* Creating an S3 bucket for static website hosting
* Configuring the bucket to allow public access
* Uploading HTML files (index and error pages)
* Outputting the website endpoint

---

## 🛠️ Tech Stack

* **Terraform** for infrastructure provisioning
* **AWS S3** for static website hosting
* **VS Code** as the local development environment
* **HTML** files for website content

---

## 🗂️ Project Structure

```
mys3staticwebsite/
├── index.html
├── error.html
├── main.tf
├── variables.tf
├── outputs.tf
├── provider.tf
```

---

## ⚙️ Setup and Configuration

### 🔥 Step 1: Configure AWS Provider

```hcl
# provider.tf
provider "aws" {
  region = var.region
}
```

---

### ⚙️ Step 2: Define Variables

```hcl
# variables.tf
variable "region" {
  description = "AWS region to deploy resources"
  type        = string
  default     = "eu-central-1"
}

variable "bucket_name" {
  description = "Unique name for the S3 bucket"
  type        = string
  default     = "mywebsite2025-unique123"  # <-- Replace with your unique bucket name
}
```

---

### 🪣 Step 3: Create and Configure S3 Bucket

```hcl
# main.tf
resource "aws_s3_bucket" "website_bucket" {
  bucket = var.bucket_name

  website {
    index_document = "index.html"
    error_document = "error.html"
  }
}

resource "aws_s3_bucket_ownership_controls" "ownership" {
  bucket = aws_s3_bucket.website_bucket.id

  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}

resource "aws_s3_bucket_public_access_block" "public_access" {
  bucket = aws_s3_bucket.website_bucket.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

resource "aws_s3_bucket_acl" "bucket_acl" {
  depends_on = [
    aws_s3_bucket_ownership_controls.ownership,
    aws_s3_bucket_public_access_block.public_access
  ]
  bucket = aws_s3_bucket.website_bucket.id
  acl    = "public-read"
}

resource "aws_s3_bucket_policy" "public_policy" {
  bucket = aws_s3_bucket.website_bucket.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "PublicReadGetObject"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.website_bucket.arn}/*"
      }
    ]
  })
}
```

---

### 🌐 Step 4: Upload HTML Files

```hcl
resource "aws_s3_bucket_object" "index" {
  bucket       = aws_s3_bucket.website_bucket.id
  key          = "index.html"
  source       = "index.html"
  content_type = "text/html"
}

resource "aws_s3_bucket_object" "error" {
  bucket       = aws_s3_bucket.website_bucket.id
  key          = "error.html"
  source       = "error.html"
  content_type = "text/html"
}
```

---

### ✅ Step 5: Outputs

```hcl
# outputs.tf
output "website_url" {
  description = "S3 static website endpoint"
  value       = aws_s3_bucket.website_bucket.website_endpoint
}
```

---

## 💻 HTML Files

### index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Static Website</title>
  </head>
  <body>
    <h1>Hello from Tshidi’s S3 static website!</h1>
  </body>
</html>
```

---

### error.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Error</title>
  </head>
  <body>
    <h1>Oops! Page not found.</h1>
  </body>
</html>
```

---

## 🚀 Deployment Steps

```bash
terraform init
terraform plan
terraform apply
```

* After successful deployment, Terraform will output your **website URL**.
* Open the URL in your browser to view your static website.

---

## ✅ Conclusion

In this project, we learned how to:

* Use Terraform to provision an AWS S3 bucket
* Configure static website hosting and public access
* Upload website content to the bucket

You can now easily update your HTML files and redeploy with Terraform to manage your website infrastructure as code.

---

### ⭐ Feel free to fork, clone, and experiment!

---

⚡ **Let me know if you'd also like me to include Terraform diagram visuals or a badge for "Deploy with Terraform"!**
