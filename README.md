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

```

## 🎯 Objective

The objective of this project was to build an automated data enrichment and notification pipeline using n8n.

Instead of manually processing each record, the workflow automatically:

1. Retrieves data from Google Sheets.
2. Extracts the person's name.
3. Sends the name to the Genderize API.
4. Retrieves an estimated age using the Agify API.
5. Retrieves likely country associations using the Nationalize API.
6. Maps the API responses into the appropriate Google Sheets columns.
7. Updates the original record.
8. Sends an automated email containing the enriched information.

## 🛠️ Technologies Used

| Technology             | Purpose                                   |
| ---------------------- | ----------------------------------------- |
| **n8n**                | Workflow automation and API orchestration |
| **Google Sheets**      | Input and output data source              |
| **HTTP Request Nodes** | REST API integration                      |
| **Genderize.io**       | Statistical gender prediction             |
| **Agify.io**           | Statistical age estimation                |
| **Nationalize.io**     | Statistical nationality prediction        |
| **Gmail**              | Automated email notification              |
| **JSON**               | API response and data exchange            |

## 🔄 Workflow Breakdown

1. Google Sheets - Get Rows

The workflow begins by connecting to a Google Sheets document.

Example input:

| ID | Name  | Number | Budget | Gender | Nationality | Age |
| -- | ----- | ------ | ------ | ------ | ----------- | --- |
| 1  | Divya | 12345  | $50k   |        |             |     |

2. Genderize.io API

The person's name is dynamically passed to the Genderize API using an HTTP GET request.

Endpoint
https://api.genderize.io

Query Parameter
name={{ $json.name }}

Example response:

{
  "name": "Divya",
  "gender": "female",
  "probability": 0.99,
  "count": 7737
}

The workflow extracts the predicted gender and uses it to enrich the original record.

Genderize provides a probability score, meaning the result represents a statistical prediction rather than a verified personal attribute.

API Documentation: https://genderize.io/documentation/api/reference













