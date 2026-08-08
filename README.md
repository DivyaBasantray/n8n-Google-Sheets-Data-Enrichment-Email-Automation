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

Endpoint:

https://api.genderize.io

Query Parameter:

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

3. Agify.io API

The name is then passed to the Agify API to obtain an estimated age.

Endpoint:

https://api.agify.io

Query Parameter:

name={{ $json.name }}

Example response:

{
  "name": "Divya",
  "age": 32,
  "count": 1287
}

The returned age is a statistical estimate based on patterns associated with the name.

For example, when testing the name Divya, the API estimated an age of 32, even though the actual age of the person used for testing was 25.

This demonstrates an important limitation of name-based statistical prediction.

API Documentation:
https://agify.io/documentation/api/reference

4. Nationalize.io API

The same name is sent to the Nationalize API to obtain likely country associations.

Endpoint:

https://api.nationalize.io

Query Parameter:

name={{ $json.name }}

Example response:

{
  "name": "Divya",
  "country": [
    {
      "country_id": "IN",
      "probability": 0.713
    },
    {
      "country_id": "US",
      "probability": 0.063
    }
  ]
}

The API returns multiple possible countries along with their probabilities.

The workflow uses the relevant country information to enrich the Google Sheets record.

API Documentation:
https://nationalize.io/documentation/api/reference

5. Google Sheets - Append / Update Row

After receiving the results from the three APIs, the workflow maps the enriched information back to the corresponding Google Sheets record.

Example Output:

ID	Name	Number	Budget	Gender	Nationality	Age
1	Divya	12345	$50k	female	IN	32

The record is automatically updated without manually entering the additional information.

6. Automated Email Notification

After the Google Sheets record is updated, the workflow triggers an email notification using the Gmail node.

The email contains the enriched information generated by the workflow.

Example:

Subject: New Client

Hey Divya,

A new client has signed up. The details are below.

Number: 12345
Budget: $50k

Predicted gender: female
Estimated age: 32
Predicted nationality: IN

Do make sure to connect with her at 12345.

Thanks!

The email is generated automatically after the workflow completes the API enrichment and spreadsheet update.

This creates an end-to-end workflow where the data is not only enriched but also automatically communicated to the relevant recipient.

## 📊 Before vs After

Before Automation

ID | Name  | Number | Budget | Gender | Nationality | Age
----------------------------------------------------------
1  | Divya | 12345  | $50k   |        |              |

After Automation

ID | Name  | Number | Budget | Gender | Nationality | Age
----------------------------------------------------------
1  | Divya | 12345  | $50k   | female | IN           | 32

An automated email notification is then sent containing the enriched information.


## 🧠 Key Concepts Demonstrated

1. Workflow Automation
2. REST API Integration
3. HTTP GET Requests
4. API Orchestration
5. Google Sheets Integration
6. Gmail Integration
7. JSON Data Handling
8. Dynamic Data Mapping
9. Data Enrichment
10. API Response Parsing
11. Automated Notifications
12. External Service Integration
13. No-Code / Low-Code Automation
14. Workflow Design


## ⚙️ How It Works
                ┌─────────────────────┐
                │    Google Sheets    │
                │    Input Dataset    │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │   Get Rows in Sheet │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │   Genderize.io API  │
                │  Gender Prediction  │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │     Agify.io API    │
                │    Age Estimation   │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │  Nationalize.io API │
                │Nationality Prediction│
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │    Google Sheets    │
                │  Append / Update    │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │      Gmail Node     │
                │  Email Notification │
                └─────────────────────┘

## 🔐 Security

- Never commit credentials, API keys, OAuth tokens, or private spreadsheet information to GitHub.

Before uploading the n8n workflow JSON:

- Remove or replace credential references where necessary.
- Do not include private Google Sheets data.
- Use dummy/sample data for demonstration.
- Never expose API keys in screenshots.
- Never expose Gmail credentials or OAuth tokens.

## ⚠️ Limitations

- The APIs used in this project provide statistical predictions, not verified personal information.

Age

- Agify estimates age based on statistical patterns associated with names. The predicted age can therefore differ significantly from someone's actual age.

Gender

- Genderize predicts the gender statistically associated with a name. It should not be interpreted as determining an individual's actual gender identity.

Nationality

- Nationalize predicts countries statistically associated with a name. A person's name does not necessarily indicate their actual nationality.

- Therefore, the results should be treated as API-generated estimates and probabilities, not factual personal attributes.

The primary purpose of this project is to demonstrate:

- API integration
- Data enrichment
- Workflow automation
- Dynamic data processing
- Automated notifications


## 🚀 Future Improvements

Potential improvements include:

- Add API error handling
- Add retry logic for failed requests
- Handle API rate limits
- Validate empty or invalid names
- Store prediction probabilities
-Convert country codes into country names
- Process multiple spreadsheet records
- Add duplicate detection
- Add workflow execution logging
- Schedule automatic workflow execution
- Add conditional email notifications
- Add confidence thresholds
- Add data validation before updating Google Sheets
- Add Slack or Microsoft Teams notifications
- Create a dashboard for monitoring workflow executions

## 📚 APIs & Resources

- Genderize.io: https://genderize.io/
- Agify.io: https://agify.io/
- Nationalize.io: https://nationalize.io/
- n8n Documentation: https://docs.n8n.io/

  
## 👩‍💻 Project Summary

- This project demonstrates how n8n can be used to orchestrate multiple APIs, enrich structured data, update a data source, and trigger automated notifications from a single workflow.

The complete pipeline combines:

Google Sheets
      +
n8n
      +
REST APIs
      +
JSON Data
      +
Dynamic Mapping
      +
Gmail
      ↓
Automated Data Enrichment & Notification

- The project was built as a practical demonstration of workflow automation, API integration, data processing, external service orchestration, and automated communication.
