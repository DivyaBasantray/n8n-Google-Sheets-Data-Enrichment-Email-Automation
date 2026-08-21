# Real Estate Client Data Enrichment Automation

An **n8n automation workflow** that enriches real estate client data using external APIs, updates the results in Google Sheets, and sends the final client information through email.

The workflow starts with basic client details such as **ID, name, phone number, and budget**. It then uses external APIs to automatically fetch the client's **gender and estimated age** based on their name, stores the enriched information back in Google Sheets, and sends the details through email.

---

## Project Overview

In a real estate workflow, client information may initially contain only basic details:

* Client ID
* Name
* Phone Number
* Budget

Instead of manually researching or entering additional information, this workflow automates the enrichment process.

Using **n8n**, the workflow:

1. Reads client records from Google Sheets.
2. Sends the client's name to a gender prediction API.
3. Sends the client's name to an age prediction API.
4. Combines the API results with the original client information.
5. Updates the enriched record in Google Sheets.
6. Sends the completed client details through Gmail.

This creates a simple end-to-end data enrichment pipeline with minimal manual intervention.

---

## Workflow

```text
Manual Trigger
      ↓
Get Rows from Google Sheets
      ↓
Gender API Request
      ↓
Age API Request
      ↓
Append / Update Row in Google Sheets
      ↓
Send Client Details via Gmail
```

---

## Tools & Technologies

* **n8n** - Workflow automation and orchestration
* **Google Sheets** - Client data storage
* **Genderize API** - Gender prediction based on name
* **Agify API** - Age prediction based on name
* **HTTP Request nodes** - API integration
* **Gmail** - Automated email notification

---

## Input Data

The initial Google Sheet contains basic client information:

| ID | Name           | Number | Budget |
| -- | -------------- | ------ | ------ |
| 1  | Divya          | 89539  | $50k   |
| 2  | Carlos Mendoza | 67890  | $35k   |
| 3  | Yuki Tanaka    | 24681  | $72k   |

The workflow uses the **Name** field as the primary input for the external APIs.

---

## API-Based Data Enrichment

### 1. Gender Prediction

The workflow sends the client's name to the **Genderize API** through an HTTP Request node.

Example:

```text
GET https://api.genderize.io/
```

The client's name is passed dynamically as a query parameter:

```text
name = {{$json.Name}}
```

The API returns information such as:

```json
{
  "name": "Divya",
  "gender": "female",
  "probability": 0.99
}
```

The workflow extracts the `gender` value and passes it to the next step.

---

### 2. Age Prediction

The client's name is then sent to the **Agify API**.

Example:

```text
GET https://api.agify.io/
```

The name is again passed dynamically:

```text
name = {{$json.Name}}
```

Example response:

```json
{
  "count": 1287,
  "name": "Divya",
  "age": 32
}
```

The workflow extracts the estimated `age`.

---

## Enriched Google Sheet

After both API requests are completed, the original client record is combined with the API results.

The final Google Sheet contains:

| ID | Name           | Number | Budget | Gender | Age |
| -- | -------------- | ------ | ------ | ------ | --- |
| 1  | Divya          | 89539  | $50k   | female | 32  |
| 2  | Carlos Mendoza | 67890  | $35k   | male   | 51  |
| 3  | Yuki Tanaka    | 24681  | $72k   | female | 40  |

The **Append or Update Row** node maps the existing client fields along with the newly retrieved gender and age values.

This prevents the API results from remaining isolated and keeps the enriched information stored with the original client record.

---

## Automated Email Notification

Once the Google Sheet has been updated, the workflow sends the client details through **Gmail**.

The email is dynamically generated using the values from the workflow.

Example:

```text
Hey Divya,

A new client Erik Johansson has signed up. The details are below.

Number: 98765
Budget: $61k

Below are the expected gender and age:

Gender: male
Age: 51

Do make sure to connect with him at 98765.

Thanks,
Your awesome workflow.
```

Because the email uses n8n expressions, the content changes automatically for every client record.

---

## Key n8n Concepts Used

### 🔹 Google Sheets Integration

Used to read existing client records and write the enriched results back to the spreadsheet.

### 🔹 HTTP Request Nodes

Used to communicate with external APIs and retrieve additional client attributes.

### 🔹 Dynamic Expressions

n8n expressions are used to pass values between nodes dynamically.

For example:

```text
{{$json.Name}}
```

and:

```text
{{$('HTTP Gender Request').item.json.gender}}
```

This allows each client's information to flow through the workflow without manually entering values.

### 🔹 Data Enrichment

The workflow demonstrates how incomplete records can be enriched with information obtained from external APIs.

### 🔹 Automated Notifications

The Gmail node generates and sends an email using the processed client data.

---

## What This Automation Solves

Without automation, the process would require someone to:

1. Open the client spreadsheet.
2. Look up each client's information.
3. Retrieve additional details from external sources.
4. Update the spreadsheet manually.
5. Prepare an email.
6. Send the client information.

With n8n, these steps are connected into a single workflow.

**Input → API Enrichment → Data Update → Email Notification**

This reduces repetitive work and keeps the client information consistent across the workflow.

---

## Workflow Outcome

The final automation provides a simple example of how **n8n can connect spreadsheets, external APIs, and email services into one automated data pipeline**.

It demonstrates practical use of:

* API integration
* Data enrichment
* Dynamic data mapping
* Google Sheets automation
* Email automation
* Workflow orchestration

The project is built around a real estate use case, but the same approach can be adapted for other business workflows where existing records need to be enriched with information from external APIs.

---

## Project Structure

```text
Real-Estate-Client-Data-Enrichment/
│
├── README.md
└── workflow/
    └── n8n-workflow.json
```

> Export the n8n workflow as a `.json` file and place it inside the `workflow` folder if you want others to be able to import and test the automation.

---

## Important Limitations

The APIs used in this project provide statistical predictions and estimates, not verified personal information.

🔹Age: The estimated age may differ significantly from the person's actual age.

🔹Gender: The API predicts the gender statistically associated with a name and does not determine an individual's actual gender identity.

For example, the API estimated the age associated with the name Divya as 32, while the actual age used for testing was 25.

This highlights why API-generated predictions should be treated as estimates rather than factual personal attributes.

The primary purpose of this project is to demonstrate API integration, data enrichment, workflow automation, and automated communication.

---

## Key Takeaway

This project shows how **n8n can act as the bridge between business data, external APIs, and communication tools**.

A basic client record enters the workflow, external APIs enrich the data, Google Sheets stores the updated information, and Gmail delivers the final result automatically.

**One workflow. Multiple integrations. Less manual work.**
