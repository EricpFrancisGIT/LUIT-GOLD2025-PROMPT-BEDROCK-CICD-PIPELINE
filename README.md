# 🧠 Prompt Deployment Pipeline using Amazon Bedrock & GitHub Actions

Pixel Learning Co. | AI-Powered Content Generation & Publishing

## 📚 Overview

Pixel Learning Co. is a digital-first education startup focused on streamlining the creation and publishing of student- and instructor-facing content. This repository implements a fully automated, GitHub-based CI/CD pipeline that renders AI prompt templates using Amazon Bedrock and publishes generated content to static websites hosted on Amazon S3.

---

## 🎯 Objectives

- ✅ **Automate Prompt Rendering** using structured templates and variable configs
- ✅ **Run Real-Time Inference** with Claude 3 Sonnet via Amazon Bedrock
- ✅ **Manage Content Workflow** using GitHub Actions (PR → Beta | Merge → Prod)
- ✅ **Publish Static Content** to S3 websites under structured paths
- ✅ **Maintain Version Control** across templates, prompts, and generated outputs

---

## 🏗️ Architecture

GitHub Repo
├── prompts/
│ └── welcome_prompt.json
├── prompt_templates/
│ └── welcome_email.txt
├── outputs/
│ └── welcome_jordan.html
├── .github/workflows/
│ ├── on_pull_request.yml → Deploys to S3 beta/
│ └── on_merge.yml → Deploys to S3 prod/
├── process_prompt.py → Main Python script
└── README.md

---

## ⚙️ Requirements

### 🔐 GitHub Secrets

Set the following secrets in your GitHub repository settings:

| Secret Name        | Description                            |
|--------------------|----------------------------------------|
| `AWS_ACCESS_KEY_ID` | IAM user's access key ID              |
| `AWS_SECRET_ACCESS_KEY` | IAM user's secret access key       |
| `AWS_REGION`        | e.g., `us-east-1`                     |
| `S3_BUCKET_BETA`    | S3 bucket for beta content (PRs)      |
| `S3_BUCKET_PROD`    | S3 bucket for prod content (merges)   |

---

## ☁️ AWS Setup

### 1. Create S3 Buckets

Create two S3 buckets for hosting static content:

- **Beta Bucket:** `pixel-claude-beta`
- **Prod Bucket:** `pixel-claude-prod`

Enable **Static Website Hosting** in each and note the **endpoint URL**.

### 2. Configure IAM Role/User

Create an IAM user or GitHub OIDC role with the following permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::pixel-claude-*",
        "arn:aws:s3:::pixel-claude-*/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel"
      ],
      "Resource": "*"
    }
  ]
}
modelId = "anthropic.claude-3-sonnet-20240229-v1:0"

body = {
    "anthropic_version": "bedrock-2023-05-31",
    "max_tokens": 1024,
    "messages": [
        {
            "role": "user",
            "content": f"""Human: {prompt}"""
        }
    ]
}
🛠️ How It Works
process_prompt.py

This script:

    Loads a prompt template from prompt_templates/

    Loads a config file from prompts/

    Renders the template using config variables

    Sends the final prompt to Amazon Bedrock

    Saves the AI-generated content to outputs/

    Uploads result to the appropriate S3 bucket prefix (beta/ or prod/)

Example S3 Upload Paths:

s3://pixel-claude-beta/beta/outputs/summary_cloud.html
s3://pixel-claude-prod/prod/outputs/summary_cloud.html

🚀 GitHub Actions Workflows
1. Pull Request Workflow - .github/workflows/on_pull_request.yml

    Trigger: Pull Request targeting main

    Action: Renders prompts and uploads to S3_BUCKET_BETA under beta/ prefix

2. Merge Workflow - .github/workflows/on_merge.yml

    Trigger: Push to main

    Action: Renders prompts and uploads to S3_BUCKET_PROD under prod/ prefix

✍️ Creating New Prompts
Add Config File

prompts/welcome_prompt.json
{
  "course_name": "Cloud Computing Fundamentals 101",
  "module_title":"Virtualization and Cloud Hypervisors",
  "topics":"Virtualization concepts, hypervisor types, cloud service models, and container orchestration",
  "output_format": "html"
}
Add Template

prompt_templates/summary_cloud.txt
You are a technical educator summarizing course content for students to learn Cloud Computing from the ground up.

Course Name: {{ course_name }}
Module: {{ module_title }}
Topics Covered: {{ topics }}

Please write a concise summary (in 1-2 paragraphs) that highlights the key takeaways from this module. Use student-friendly language.
Format the output in clean, structured HTML with headings and bullet points, so it’s easy for students to read on a webpage, use a font size with a technical theme.
Make the background color light grey.

Upon PR or merge, the generated output will be sent to Amazon Bedrock, saved locally under outputs/, and deployed to the appropriate S3 path.
🧪 Testing the Pipeline

    Fork the repository

    Push a new prompt config and template

    Open a pull request to main

    GitHub Actions will generate and deploy to the beta/ S3 website

    Merge the PR to deploy to prod/

🔍 Viewing Results

After deployment, access your content at:
Beta Site: s3://pixel-claude-beta/beta/outputs/summary_cloud.html

Prod Site: s3://pixel-claude-prod/prod/outputs/summary_cloud.html

🧠 Why Bedrock + GitHub Actions?
Feature	Benefit
💡 Amazon Bedrock	Real-time, scalable prompt execution with multi-model support
⚙️ GitHub Actions	Fully automated, developer-friendly CI/CD pipelines
☁️ Amazon S3	Scalable, low-cost static content delivery
🔐 Secrets Management	Secure and auditable pipeline operations
🛑 Critical Note

Do NOT use provisioned throughput for this project. Use real-time inference only via:

modelId = "anthropic.claude-3-sonnet-20240229-v1:0"

📄 License

MIT License © 2025 Pixel Learning Co.
