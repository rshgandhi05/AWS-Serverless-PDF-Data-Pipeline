# AWS-Serverless-PDF-Data-Pipeline

Overview
This project solves the "Dirty Data" problem in government PDF reporting. It automates the extraction of budget line items from Congress.gov, utilizing Local AI for summarization and Custom Regex for data healing.

This pipeline was designed to be 100% Serverless and highly cost-efficient, running entirely within the AWS Free Tier.

Architecture

<img width="1832" height="1207" alt="image" src="https://github.com/user-attachments/assets/b9c357ed-b454-4d4c-8eb6-024df7571556" />


The system is built with a Cloud-Native mindset:

Trigger: Amazon EventBridge (Scheduled CRON) initiates the workflow.

Compute: AWS Lambda (Python 3.12) processes the data.

Storage: Amazon S3 (Data Lake Architecture) stores both Raw and Processed states.

AI Engine: DistilBART (Hugging Face) running locally on CPU via Lambda Layers.

Key Engineering Wins
Normalization Engine: Implemented custom Regex logic to "stitch" words broken by PDF page margins (e.g., healing "Inter-national" into "International").

Context Slicing: Developed a sliding window algorithm to capture exactly 150 words of context around financial matches, ensuring data accuracy.

Privacy-First AI: Packaged a local LLM into a Lambda Layer. No data leaves the AWS environment, and there are $0 API costs.

WAF Resiliency: Implemented User-Agent rotation to ensure authorized, reliable access to government servers.

Technical Stack
Cloud: AWS (Lambda, S3, EventBridge, IAM, CloudWatch)

Languages: Python 3.12

Libraries: PyPDF2, Requests, Regex (re), Hugging Face Transformers

DevOps: GitHub for version control and documentation

Implementation Details
Lambda Layers: Optimized deployment by moving large dependencies (transformers, pypdf) into a shared Layer.

Environment Variables: Used for secure configuration of S3 bucket names and target URLs.

Permissions: Followed the Principle of Least Privilege using custom IAM roles.

Results
The pipeline transforms a 100+ page unstructured PDF into actionable JSON insights in under 8 seconds.

JSON

{
  "page_number": 42,
  "found_phrase": "International Broadcasting Operations",
  "extracted_numbers": [681448000.0],
  "ai_summary": "The budget allocates $681.4M for operations, a 5.2% increase from the previous year."
}
