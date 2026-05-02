# CineFlow — Pipeline ETL Cloud-Native su Azure

![Azure](https://img.shields.io/badge/Microsoft%20Azure-Cloud-0078D4?logo=microsoftazure&logoColor=white)
![ADF](https://img.shields.io/badge/Azure%20Data%20Factory-ETL-0089D6)
![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)
![Blob Storage](https://img.shields.io/badge/Blob%20Storage-Landing%20Zone-blue)

## Panoramica

Pipeline ETL end-to-end su Microsoft Azure che ingerisce un dataset grezzo di film, applica filtri e trasformazioni con Azure Data Factory, e consegna un output pulito — con l'intera infrastruttura definita come codice tramite Terraform.

Il progetto segue un pattern di architettura a tre zone di storage (`input` → `staging` → `output`) tipico delle soluzioni enterprise di Data Engineering su cloud Azure, riproducibile e distruttibile con un singolo comando.

## Valore Enterprise

| Settore / Azienda | Rilevanza |
|-------------------|-----------|
| Utilities & Energy (Enel, Terna) | Pattern ETL cloud per integrazione dati operativi su Azure |
| IT Consulting (NTT Data, Accenture) | IaC Terraform + ADF: stack standard di delivery cloud |
| Data Engineering (Data Reply) | Architettura multi-stage con separazione raw/staging/output |
| Engineering Informatica | Pipeline Azure deployabile come componente di soluzioni enterprise |

## Architettura

```
[Blob: input]  →  ADF Data Flow (Filter + Remap)  →  [Blob: staging]  →  ADF Copy Activity  →  [Blob: output]
```

| Stage | Servizio Azure | Operazione |
|-------|----------------|-----------|
| Ingestion | Blob Storage (`input`) | Landing zone immutabile per dati grezzi |
| Trasformazione | ADF Data Flow (Apache Spark) | Filtro `Rating > 7`, remap colonne EN→IT |
| Staging | Blob Storage (`staging`) | Buffer intermedio validato |
| Load finale | ADF Copy Activity | Consegna con preservazione metadati |

## Decisioni Architetturali

- **3 container distinti**: raw immutabile, staging ispezionabile, output finale — debugging isolato per stage
- **Data Flow vs Copy Activity**: trasformazione complessa su backend Spark gestito, non un semplice file copy
- **Terraform IaC**: Resource Group, Storage Account e ADF deployati e distrutti con `terraform apply/destroy`

## Setup e Deploy

```bash
git clone https://github.com/sylver86/02-azure-data-pipeline-terraform-adf.git
cd 02-azure-data-pipeline-terraform-adf
az login

cd terraform
terraform init && terraform plan && terraform apply
```

Dopo il deploy: caricare `moviesDB.csv` nel container `input`, avviare la pipeline in ADF Studio.
Per evitare costi: `terraform destroy` al termine.

## Stack Tecnologico

`Microsoft Azure` · `Azure Data Factory` · `Azure Blob Storage` · `Terraform ≥ 1.0` · `Apache Spark (via ADF Data Flow)` · `Azure CLI`

---

---

# CineFlow — Cloud-Native ETL Pipeline on Azure 🇬🇧

![Azure](https://img.shields.io/badge/Microsoft%20Azure-Cloud-0078D4?logo=microsoftazure&logoColor=white)
![ADF](https://img.shields.io/badge/Azure%20Data%20Factory-ETL-0089D6)
![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)

## Overview

End-to-end ETL pipeline on Microsoft Azure that ingests a raw movie dataset, filters and remaps it via Azure Data Factory, and delivers a clean output — entire infrastructure defined as code with Terraform.

Implements a three-zone storage pattern (`input` → `staging` → `output`) standard in enterprise Azure Data Engineering: reproducible, auditable, and tear-down safe.

## Architecture

```
[Blob: input]  →  ADF Data Flow (Filter + Remap)  →  [Blob: staging]  →  ADF Copy Activity  →  [Blob: output]
```

| Stage | Azure Service | What happens |
|-------|--------------|--------------|
| Ingestion | Blob Storage (`input`) | Raw `moviesDB.csv` as immutable landing zone |
| Transformation | ADF Data Flow (Spark) | Filter `Rating > 7`, remap 3 columns EN→IT |
| Staging | Blob Storage (`staging`) | Validated intermediate, available for inspection |
| Final Load | ADF Copy Activity | Clean file delivered to `output` |

## Architectural Decisions

- **3-container design**: separation of raw / staging / output isolates bugs to a single stage
- **Data Flow over Copy Activity**: filtering and remapping assigned to Spark-backed Data Flow for scalability
- **Terraform IaC**: all Azure resources reproducible and destroyable with a single command

## Setup & Deployment

```bash
git clone https://github.com/sylver86/02-azure-data-pipeline-terraform-adf.git
cd 02-azure-data-pipeline-terraform-adf
az login

cd terraform
terraform init && terraform plan && terraform apply
```

Upload `moviesDB.csv` to the `input` container, trigger the pipeline in ADF Studio, verify `output` container.
Cleanup: `terraform destroy` (avoids unexpected Azure costs).

## Technologies

`Microsoft Azure` · `Azure Data Factory` · `Azure Blob Storage` · `Terraform ≥ 1.0` · `Apache Spark` · `Azure CLI`
