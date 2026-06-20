# Week 4 - Azure Data Factory Data Pipeline

## Task

Understand Azure cloud concepts and build an end-to-end data pipeline using Storage Account and Azure Data Factory. Steps: 1. Explore Azure Portal and create a Resource Group. 2. Create a Storage Account, Blob Container, and upload a CSV file. 3. Create Azure Data Factory and explore ADF UI (Author, Monitor, Manage). 4. Create Linked Service (Blob Storage) and datasets (source and destination). 5. Use Get Metadata activity to retrieve file information. 6. Build a pipeline using Copy Data activity and configure source and destination. 7. Execute the pipeline using Debug/Trigger and monitor execution. 8. Assign IAM roles (Reader, Contributor) and ensure access between ADF and Storage. 9. Implement an end-to-end pipeline (Blob → ADF → Destination) with metadata validation. Output: Screenshots (Resource Group, Storage, ADF, Pipeline) + pipeline execution results + brief summary.

## Dataset

- **Source:** [Sample Superstore](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final?select=Sample+-+Superstore.csv)
- **File size:** ~2.29 MB
- **Columns:** 21 (Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Customer Name, Segment, Country, City, State, Postal Code, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount, Profit)

## Azure Resources Used

| Resource | Name | Region | Details |
|---|---|---|---|
| Subscription | Azure for Students | — | Student subscription |
| Resource Group | `Week-4` | Central India | Groups all resources for this task |
| Storage Account | `forweek4` | East Asia | Standard, RA-GRS, Hot tier |
| Blob Container | `week4container` | — | Private access, holds input & output folders |
| Data Factory (V2) | `madeinweek4` | East Asia | Public endpoint, AutoResolveIntegrationRuntime |

## What I Did

### 1. Created a Resource Group

Made a resource group called `Week-4` in Central India to keep all the resources for this task together.

![Resource Group — Review + Create](screenshots/Screenshot%20from%202026-06-20%2001-10-11.png)

---

### 2. Created a Storage Account

Set up a storage account named `forweek4` in East Asia with standard performance and RA-GRS replication.

![Storage Account — Review + Create](screenshots/Screenshot%20from%202026-06-20%2001-15-31.png)

---

### 3. Created a Blob Container & Uploaded the CSV

Created a private container called `week4container` inside the storage account.

![Blob Container — week4container](screenshots/Screenshot%20from%202026-06-20%2001-17-03.png)

Uploaded `Sample - Superstore.csv` into the `input_week4` folder inside the container.

![CSV upload — input_week4 folder](screenshots/Screenshot%20from%202026-06-20%2001-20-14.png)

---

### 4. Created Azure Data Factory

Created a Data Factory (V2) named `madeinweek4` in the Week-4 resource group, East Asia region.

![Data Factory — Review + Create](screenshots/Screenshot%20from%202026-06-20%2001-26-22.png)

---

### 5. Configured IAM — Managed Identity & Role Assignment

Turned on the system-assigned managed identity for `madeinweek4` so ADF can securely access the storage account.

![Managed Identity — turned on for madeinweek4](screenshots/Screenshot%20from%202026-06-20%2002-32-11.png)

Then went to the `forweek4` storage account's IAM and assigned the **Storage Blob Data Contributor** role to ADF's managed identity.

![Role Assignment — selecting madeinweek4 managed identity](screenshots/Screenshot%20from%202026-06-20%2002-34-56.png)

![Role Assignment — Review + Assign](screenshots/Screenshot%20from%202026-06-20%2002-36-04.png)

> **Note:** Since my Azure for Students subscription has organization-level restrictions on creating new users, I assigned the IAM role directly between the ADF managed identity and the storage account instead of creating a separate user for it.

---

### 6. Created a Linked Service

Created a linked service `Azureblob_in_week4` in ADF Studio to connect to the `forweek4` storage account using account key auth.

![Linked Service — Azureblob_in_week4](screenshots/Screenshot%20from%202026-06-20%2001-31-56.png)

---

### 7. Created Datasets (Source & Destination)

Created the source dataset `SampleSuperstoreDataset` pointing to `week4container/input_week4/Sample - Superstore.csv`.

![Source Dataset — SampleSuperstoreDataset](screenshots/Screenshot%20from%202026-06-20%2001-38-50.png)

Created the output dataset `SampleSuperstoreOutputDataset` pointing to `week4container/output_week4/`.

![Output Dataset + Pipeline canvas — SampleSuperstoreOutputDataset](screenshots/Screenshot%20from%202026-06-20%2002-11-00.png)

---

### 8. Built the Pipeline

Built `pipeline1` with a Get Metadata activity followed by a Copy Data activity, plus Set Variable activities for handling success and failure on both steps.

```
Get Metadata1
    ├── [On Success] → Copydataofsample
    │                      ├── [On Success] → Set Variable: "for pipeline success"
    │                      └── [On Failure] → Set Variable: "for failure of copy data"
    └── [On Failure] → Set Variable: "for failure of get metadata"
```

Configured the Copy Data source to read from `SampleSuperstoreDataset`.

![Pipeline — Copy Data source config](screenshots/Screenshot%20from%202026-06-20%2002-19-57.png)

Configured the sink to write to `SampleSuperstoreOutputDataset`.

![Pipeline — Copy Data sink config](screenshots/Screenshot%20from%202026-06-20%2002-20-03.png)

Set up Get Metadata to check for file existence, size, last modified date, and column structure.

![Pipeline — Get Metadata settings](screenshots/Screenshot%20from%202026-06-20%2002-23-31.png)

> The exported pipeline JSON is available in [`templates/copy_pipeline.json`](templates/copy_pipeline.json).

---

### 9. Pipeline Execution & Verification

Ran the pipeline in debug mode — all 3 activities (Get Metadata, Copy Data, Set Variable) succeeded.

![Pipeline debug run — all succeeded](screenshots/Screenshot%20from%202026-06-20%2002-26-35.png)

The copy activity transferred 2.288 MB in 12 seconds at 1.144 MB/s throughput.

![Copy activity details — 2.288 MB transferred](screenshots/Screenshot%20from%202026-06-20%2002-25-37.png)

Get Metadata confirmed the file exists and returned the full column structure (all 21 columns).

![Get Metadata output — part 1](screenshots/Screenshot%20from%202026-06-20%2002-26-11.png)
![Get Metadata output — part 2](screenshots/Screenshot%20from%202026-06-20%2002-26-15.png)

---

### 10. Verified Output in Blob Storage

Checked `week4container/output_week4/` and the copied `Sample - Superstore.csv` was there (2.18 MiB, Block blob, Hot tier).

![Output verification — file in output_week4](screenshots/Screenshot%20from%202026-06-20%2002-38-52.png)

## Pipeline Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Azure Resource Group: Week-4                │
│                                                                     │
│  ┌──────────────────────┐         ┌──────────────────────────────┐  │
│  │  Storage Account:    │         │  Azure Data Factory:         │  │
│  │  forweek4            │◄───────►│  madeinweek4                 │  │
│  │                      │  Linked │                              │  │
│  │  ┌────────────────┐  │ Service │  ┌────────────────────────┐  │  │
│  │  │ week4container │  │         │  │ pipeline1              │  │  │
│  │  │                │  │         │  │                        │  │  │
│  │  │ input_week4/   │──┼────────►│  │ Get Metadata ──►Copy  │  │  │
│  │  │  Superstore.csv│  │  Read   │  │                  Data │  │  │
│  │  │                │  │         │  │                   │    │  │  │
│  │  │ output_week4/  │◄─┼────────┤  │                   ▼    │  │  │
│  │  │  Superstore.csv│  │  Write  │  │              output/   │  │  │
│  │  └────────────────┘  │         │  └────────────────────────┘  │  │
│  └──────────────────────┘         └──────────────────────────────┘  │
│            ▲                                    │                    │
│            │         IAM Role Assignment        │                    │
│            └──── Storage Blob Data Contributor ──┘                   │
│                   (Managed Identity)                                 │
└─────────────────────────────────────────────────────────────────────┘
```

## Key Takeaways

- **Get Metadata is essential** — it acts as a pre-check before running the Copy activity, preventing failures if the source file is missing or modified.
- **Error handling matters** — the Set Variable activities for failure paths ensure pipeline observability. In a real scenario, these could trigger alerts or retry logic.
- **ADF auto-creates output folders** — the `output_week4` folder didn't exist beforehand; the Copy Data activity created it automatically during execution.

## Repository Structure

```
Week_4/
├── README.md                          ← This file
├── templates/
│   └── copy_pipeline.json             ← Exported ADF pipeline JSON
└── screenshots/
    ├── Screenshot from ... 01-10-11   ← Resource Group creation
    ├── Screenshot from ... 01-15-31   ← Storage Account creation
    ├── Screenshot from ... 01-17-03   ← Blob Container creation
    ├── Screenshot from ... 01-20-14   ← CSV upload to input_week4
    ├── Screenshot from ... 01-26-22   ← Data Factory creation
    ├── Screenshot from ... 02-32-11   ← Managed Identity setup
    ├── Screenshot from ... 02-34-56   ← IAM role assignment (Members)
    ├── Screenshot from ... 02-36-04   ← IAM role assignment (Review)
    ├── Screenshot from ... 01-31-56   ← Linked Service creation
    ├── Screenshot from ... 01-38-50   ← Source Dataset setup
    ├── Screenshot from ... 02-11-00   ← Output Dataset + pipeline canvas
    ├── Screenshot from ... 02-19-57   ← Pipeline — Source config
    ├── Screenshot from ... 02-20-03   ← Pipeline — Sink config
    ├── Screenshot from ... 02-23-31   ← Pipeline — Get Metadata settings
    ├── Screenshot from ... 02-25-37   ← Copy activity run details
    ├── Screenshot from ... 02-26-11   ← Get Metadata output (part 1)
    ├── Screenshot from ... 02-26-15   ← Get Metadata output (part 2)
    ├── Screenshot from ... 02-26-35   ← Pipeline debug — all succeeded
    └── Screenshot from ... 02-38-52   ← Output verification in Blob Storage
```