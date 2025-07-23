# 🚀 Static Website CI/CD Pipeline using GitHub Actions & AWS S3

This project demonstrates how to automatically deploy a static website (HTML/CSS/JavaScript) to **Amazon S3** using **GitHub Actions** as a CI/CD pipeline.

---

## 📁 Project Structure
```bash
static-website/
├── index.html
├── style.css
├── script.js
├── .github/
│ └── workflows/
│ └── deploy.yml
└── README.md
```

---

## 🧱 Prerequisites

### ✅ AWS Setup

1. **Create an S3 Bucket**
   - Enable **static website hosting**
   - Make it public or integrate with **CloudFront** for secure access

2. **Create an IAM User**
   - Enable **programmatic access**
   - Attach `AmazonS3FullAccess` policy (demo only; use least privilege for production)

3. **Bucket Policy (for public access)**
```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::your-bucket-name/*"
       }
     ]
   }
```

---
## 🔐 GitHub Secrets Configuration

Go to **Repository Settings > Secrets and Variables > Actions** in your GitHub repository, and add the following `New repository secrets`:

| Secret Name             | Description                                       |
|-------------------------|---------------------------------------------------|
| `AWS_ACCESS_KEY_ID`     | IAM user access key from your AWS account         |
| `AWS_SECRET_ACCESS_KEY` | IAM user secret key from your AWS account         |
| `AWS_REGION`            | AWS region where your S3 bucket is hosted (e.g., `us-east-1`) |
| `S3_BUCKET_NAME`        | The name of your S3 bucket (e.g., `my-static-site`) |

---
## ⚙️ CI/CD Workflow Configuration

Create a workflow file at `.github/workflows/deploy.yml` with the following content:

```yaml
# Workflow Name
name: Deploy to S3  

# Trigger: When to Run
on:             
  push:
    branches:
      - main  # or 'master'

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup AWS CLI
      uses: aws-actions/configure-aws-credentials@v3
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Sync files to S3
      run: |
        aws s3 sync . s3://${{ secrets.AWS_BUCKET_NAME }} --delete --exclude ".git/" --exclude ".github/"
```


📈 What This Workflow Does
- Automatically deploys your static website to AWS S3
- Triggered every time you push to the main branch
- No need to deploy manually — this is a basic CI/CD pipeline
