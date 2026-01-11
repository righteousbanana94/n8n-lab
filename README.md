# n8n Self-Hosted Google Form to GitHub Issue Automation

This repository documents a self-hosted n8n setup that automatically creates GitHub issues from Google Form submissions. The workflow conditionally handles screenshots, applies AI-based formatting when appropriate, and posts structured issues to GitHub.

---

## Overview

- Automation Platform: n8n (self-hosted)
- Deployment: Docker Compose
- Hosting: VPS (Hostinger)
- Access Method: SSH tunnel from local machine
- Trigger Source: Google Forms linked to Google Sheets
- Output Target: GitHub Issues

---

## Workflow Behavior

1. A Google Form submission adds a new row to a linked Google Sheet
2. The workflow is triggered when a new row is added
3. An IF node checks whether the submission includes a screenshot
4. Execution follows one of two paths:
   - Screenshot present: deterministic processing using code and HTTP requests
   - No screenshot: AI-assisted formatting
5. A GitHub issue is created using structured data

---

## Workflow Logic

Google Sheets Trigger (Row Added)
  ↓
IF (Screenshot Present?)
  ├─ Yes → Code (Parse Image Metadata)
  │         ↓
  │      HTTP Request (Set File Permissions on drive through API)
  │         ↓
  │      Create GitHub Issue
  │
  └─ No  → AI Agent
            ↓
        Structured Output Parser
            ↓
        Create GitHub Issue


![workflow](/resources/workflow.png)

---

## Node Breakdown

### Google Sheets Trigger
- Trigger type: When row is added
- Source: Google Form–linked spreadsheet
- Acts as the workflow entry point

![form1-1](/resources/form_example1-1.png)
![form1-2](/resources/form_example1-2.png)

### IF Node (Screenshot Detection)
- Checks whether the submission includes an image
- Routes execution to the appropriate processing path

#### Code Node (JavaScript)
- Parses Google Forms image metadata
- Extracts file IDs or URLs
- Prepares payloads for permission updates

#### HTTP Request Node
- Sends a POST request to update file permissions
- Ensures screenshots are accessible externally
- Allows images to be viewed from GitHub issues

#### Create GitHub Issue
- Creates the issue using parsed form data
- Includes a link or reference to the screenshot
- Avoids AI usage for predictable behavior


![github](/resources/github_example1-2.png)
![github](/resources/github_example1-3.png)

### Non-Screenshot Path (No Image Present)

#### AI Agent
- Processes raw form input
- Generates a clean issue title and description
- Handles summarization and normalization

#### Structured Output Parser
- Enforces a fixed schema:
  - title
  - description
- Prevents malformed or inconsistent AI output

#### Create GitHub Issue
- Posts the structured title and description to GitHub
- Uses only validated, parsed fields

---

## Deployment Architecture

- n8n is self-hosted on a VPS provided by Hostinger
- Docker and Docker Compose are used for deployment
- Persistent volumes are used for workflows and credentials

---

## Access and Security

- The n8n dashboard is not publicly exposed
- Access is provided via SSH tunneling from a local machine
- No inbound firewall ports are required for the UI
- Credentials are stored using n8n’s encrypted credential store
- Secrets are not committed to version control

---

## Docker Setup

- Deployment is managed using a Docker Compose YAML file
- The compose file defines:
  - n8n service
  - Environment variables
  - Persistent volume mappings




