# 🔄 n8n Google Sheets Data Enrichment & Email Automation

An end-to-end **n8n automation workflow** that reads records from Google Sheets, enriches them using multiple external REST APIs, updates the original spreadsheet, and automatically sends the enriched information through email.

This project demonstrates **workflow automation, REST API integration, JSON data handling, Google Sheets integration, data enrichment, dynamic field mapping, and automated email notifications**.

---

## 📌 Project Overview

The workflow starts with a Google Sheet containing basic information about a record.

For each record, the workflow uses the person's name to retrieve statistical predictions for:

- Gender
- Age
- Nationality

The enriched information is then written back to Google Sheets and an automated email is sent containing the results.

### Complete Workflow

```text
Google Sheets
      ↓
Get Rows
      ↓
Genderize.io API
      ↓
Agify.io API
      ↓
Nationalize.io API
      ↓
Append / Update Google Sheets
      ↓
Send Email
