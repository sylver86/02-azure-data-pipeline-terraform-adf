# CineFlow — Azure ETL Pipeline for Movie Data

![Azure](https://img.shields.io/badge/Microsoft%20Azure-Cloud-0078D4?logo=microsoftazure&logoColor=white)
![ADF](https://img.shields.io/badge/Azure%20Data%20Factory-ETL-blue)
![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)
![Blob Storage](https://img.shields.io/badge/Azure%20Blob%20Storage-Storage-0089D6)

## Overview

End-to-end cloud ETL pipeline on Azure that ingests a raw movie dataset, filters and remaps it, and delivers a clean output — with the entire infrastructure defined as code via Terraform.

The pipeline filters movies with rating > 7 and remaps column names from English to Italian, demonstrating a production-style separation of concerns across three storage containers: `input` → `staging` → `output`.

---

## Architecture

```
[Blob: input]
    └─► ADF Pipeline Step 1 (Data Flow)
            Filter: Rating > 7
            Remap: Title→Film, genresgenregenre→Genere, Rating→Valutazione
    └─► [Blob: staging]
            └─► ADF Pipeline Step 2 (Copy Data)
    └─► [Blob: output]
            └─► transformed_movies.csv  ✓
```

---

## Pipeline Stages

| Stage | Azure Service | What happens |
|-------|--------------|--------------|
| Ingestion | Blob Storage (`input`) | Raw `moviesDB.csv` uploaded as immutable landing zone |
| Transformation | ADF Data Flow | Filter `Rating > 7`, remap 3 columns EN→IT, Apache Spark backend |
| Staging | Blob Storage (`staging`) | Validated intermediate output held for inspection |
| Final load | ADF Copy Data Activity | Moves clean file to `output` with metadata preservation |

---

## Architectural Decisions

- **3-container design**: `input` / `staging` / `output` implements Separation of Concerns — raw data is never overwritten, bugs are isolatable to a specific stage
- **Data Flow over Copy Activity**: filtering and remapping assigned to Data Flow for scalable Spark-backed transformation rather than a simple file copy
- **Terraform for IaC**: entire infrastructure (Resource Group, Storage Account, ADF instance) defined as code — reproducible and destroyable with a single command

---

## Technologies

| Layer | Tool |
|-------|------|
| Cloud | Microsoft Azure |
| Storage | Azure Blob Storage |
| ETL / Orchestration | Azure Data Factory (Data Flow + Copy Activity) |
| Infrastructure as Code | Terraform ≥ 1.0 |
| CLI | Azure CLI |

---

## Deployment

### Prerequisites

- Azure account with active subscription
- [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli)
- [Terraform](https://developer.hashicorp.com/terraform/tutorials/azure-get-started/install-cli) ≥ 1.0

### 1. Authenticate

```bash
git clone https://github.com/sylver86/02-azure-data-pipeline-terraform-adf.git
cd 02-azure-data-pipeline-terraform-adf
az login
```

### 2. Deploy infrastructure

```bash
cd terraform
terraform init
terraform plan      # Review what will be created
terraform apply     # Type 'yes' to confirm
```

Creates: Resource Group · Storage Account (3 containers) · Azure Data Factory instance.

### 3. Run the pipeline

1. Upload `moviesDB.csv` to the **`input`** container
2. Open ADF Studio → navigate to the main pipeline → click **Debug**
3. After completion, the **`output`** container will contain `transformed_movies.csv` with only movies rated > 7 and Italian column names

### 4. Cleanup

```bash
terraform destroy   # Deletes all Azure resources — avoids unexpected costs
```

---

## Technologies

`Microsoft Azure` · `Azure Data Factory` · `Azure Blob Storage` · `Terraform` · `Apache Spark (via ADF Data Flow)` · `Azure CLI`
