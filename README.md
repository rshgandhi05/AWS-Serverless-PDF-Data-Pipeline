# AWS-Serverless-PDF-Data-Pipeline


# Project Overview
Government budget reports are often published as unstructured PDFs where page margins and formatting frequently break critical data strings (e.g., "Inter-national" split across lines). This makes standard automated extraction unreliable.

This project implements a 100% Serverless pipeline that:

Scrapes dynamic PDF reports from Congress.gov.

Heals broken text using a custom Regex Normalization Engine.

Extracts specific financial data for "International Broadcasting Operations."

Summarizes the context using a Local LLM (DistilBART) to provide actionable insights.

Total Operating Cost: $0.00 (Runs entirely within the AWS Free Tier).

# Architecture

<img width="1832" height="1207" alt="image" src="https://github.com/user-attachments/assets/b9c357ed-b454-4d4c-8eb6-024df7571556" />


# Step-by-Step Implementation
Phase 1: Authorized Extraction
Challenge: Congress.gov utilizes a Web Application Firewall (WAF) that blocks standard bot headers.

Solution: Implemented User-Agent Rotation within the Lambda script to mimic a legitimate browser handshake, ensuring reliable access to the daily dynamic PDF links.

Phase 2: Data "Healing" (Normalization Engine)
Challenge: PDF layouts often insert hyphens and newlines in the middle of keywords (e.g., Inter- \n national).

Solution: I developed a custom Regex Engine that runs before the search begins. It identifies these specific hyphenated line-break patterns and stitches the words back together, ensuring 100% recall of target phrases.

Phase 3: Sliding Window Context Slicing
Logic: Rather than just pulling a few characters, I implemented a Sliding Window Algorithm.

Process: It identifies the character index of the match, converts it to a word-index, and slices exactly 150 words of surrounding context. This provides the AI with enough information to understand why the budget numbers changed.

Phase 4: Local AI Summarization
Innovation: To avoid external API latency and costs, I packaged the DistilBART model into a Lambda Layer.

Benefit: The model runs offline on the Lambda CPU. This keeps the data strictly within the AWS environment (Privacy-First) and eliminates third-party API subscription costs.

# Key Engineering Wins
Normalization Engine: Implemented custom Regex logic to "stitch" words broken by PDF page margins (e.g., healing "Inter-national" into "International").

Context Slicing: Developed a sliding window algorithm to capture exactly 150 words of context around financial matches, ensuring data accuracy.

Privacy-First AI: Packaged a local LLM into a Lambda Layer. No data leaves the AWS environment, and there are $0 API costs.

WAF Resiliency: Implemented User-Agent rotation to ensure authorized, reliable access to government servers.

# Technical Stack
Cloud: AWS (Lambda, S3, EventBridge, IAM, CloudWatch)

Languages: Python 3.12

Libraries: PyPDF2, Requests, Regex (re), Hugging Face Transformers

DevOps: GitHub for version control and documentation

# Implementation Details
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
  "context_text_clean": "...The committee recommends an appropriation of $681,448,000 for International Broadcasting Operations...",
  "ai_summary": "The budget allocates $681.4M for operations, reflecting a 5.2% increase to support expanded digital outreach."
}

# How to Run
Clone the Repo: git clone https://github.com/your-username/repo-name

Package Layers: Zip the transformers and pypdf libraries and upload to AWS Lambda Layers.

Environment Variables: Set S3_BUCKET in the Lambda configuration.

Deploy: Upload lambda_function.py and trigger via EventBridge.
