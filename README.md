# 🚀 Static Website CI/CD Pipeline using GitHub Actions & AWS S3

## 📌 Objective:
To automate the process of building, testing, and deploying a static website (built using HTML, CSS, and JavaScript) to a web hosting platform like AWS S3 or Netlify, using modern CI/CD tools like GitHub Actions, Jenkins, or GitLab CI.

---
## 🔧 Tech Stack:
- **Frontend**: HTML, CSS, JavaScript
- **CI/CD Tool**: GitHub Actions 
- **Hosting**: AWS S3 (with optional CloudFront) 

---

## 📁 Project Structure
```bash
static-website/
├── index.html
├── style.css
├── script.js
├── .github/
│ └── workflows/
│ └── main.yml
└── README.md
```

---

## 🧱 Prerequisites

### ✅ AWS Setup

1. **Create an S3 Bucket**
   - Enable **static website hosting**
   - Make it public or integrate with **CloudFront** for secure access
     
2. **Create an IAM User**
   - Attach `AmazonS3FullAccess` policy (use least privilege for production)
   - Generate Access key

3. **Bucket Policy (for public access)**
```json
   {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cicd-static-wesite/*"
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
| `AWS_BUCKET_NAME`        | The name of your S3 bucket (e.g., `cicd-static-wesite`) |

---
## ⚙️ CI/CD Workflow Configuration

Create a workflow file at `.github/workflows/main.yml` with the following content:

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
### 🔍 Workflow Explanation

| Step                          | Purpose                                                                 |
|-------------------------------|-------------------------------------------------------------------------|
| `on: push`                    | Triggers the workflow on every push to the `main` branch               |
| `actions/checkout@v3`         | Clones your repository into the GitHub Actions runner                  |
| `configure-aws-credentials@v3`| Configures AWS CLI using credentials stored in GitHub Secrets          |
| `aws s3 sync`                 | Syncs files from the repository to the specified S3 bucket             |
| `--delete`                    | Deletes files in the S3 bucket that no longer exist in the repository  |
| `--exclude`                   | Excludes internal folders like `.git/` and `.github/` from the upload  |

### 📈 What This Workflow Does
- Automatically deploys your static website to AWS S3
- Triggered every time you push to the main branch
- No need to deploy manually — this is a basic CI/CD pipeline

---

## 🌐 Accessing the Deployed Website
After successful deployment, your static website will be accessible at the following URL format:
```
http://your-bucket-name.s3-website-<region>.amazonaws.com
```

![IAM user](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/user.png)

![bucket](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/bucket_content.png)

![output](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/output.png)

---

## ✅ Optional Add-On: Use AWS CloudFront (CDN)

To improve performance and global availability of your static website, you can use **Amazon CloudFront** as a Content Delivery Network (CDN) in front of your S3 bucket.

### 🚀 Why Use CloudFront?

- Speeds up content delivery using edge locations worldwide
- Caches your static assets (HTML, CSS, JS, images)
- Reduces load on your S3 bucket
- Optionally allows HTTPS access with a default CloudFront domain

### 🪜 Steps to Set Up CloudFront

1. **Go to AWS Console > CloudFront**
2. **Create a CloudFront distribution**
   - **Distribution name**
   - **Origin type**: AWS S3
   - **Origin**: Select your **S3 static website hosting endpoint**
     - Format: `my-bucket.s3-website-<region>.amazonaws.com`
   - **origin**: leaving it blank
   - **Viewer protocol policy**: Redirect HTTP to HTTPS *(optional)*
4. **Leave default settings for now and create the distribution**
5. **Create a Cache Invalidation**: This clears the **CloudFront cache** and ensures users see your latest deployed website files.
   - Go to your CloudFront **distribution ID**.
   - Click on the **“Invalidations”** tab.
   - Click **“Create Invalidation”**
   - In the **Object Paths**, enter:
        - `/*` : This invalidates **all files** in the distribution.
   - Click Create Invalidation

![origin](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/origin.png)

![invalidation](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/invalidation.png)


### 🌐 Accessing via CloudFront

Once the distribution is deployed (it may take a few minutes), your static site will be available at a **CloudFront URL**, like:
```
https://d3nwwpqx7m3zbn.cloudfront.net/
```

![cloudfront](https://github.com/Vaishnavi-M-Patil/static-website/blob/main/output/cloudfront.png)

---
