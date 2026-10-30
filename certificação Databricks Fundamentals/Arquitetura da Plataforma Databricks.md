# ☁️ Arquitetura da Plataforma Databricks: Visão Geral Multi-Cloud (AWS, Azure e GCP)

A plataforma **Databricks** opera nativamente nas três principais nuvens públicas (**AWS**, **Azure** e **GCP**), mantendo a mesma experiência de desenvolvimento e o mesmo motor de processamento (**Apache Spark / Photon**), mas integrando-se aos serviços nativos de armazenamento, segurança e rede de cada provedor.

---

## 📐 1. Fluxograma da Arquitetura Multi-Cloud

O diagrama abaixo ilustra a relação de desacoplamento entre o **Control Plane** (gerenciado pela Databricks) e o **Compute Plane** (hospedado dentro da conta do cliente em qualquer nuvem):

```mermaid
graph TD
    subgraph ControlPlane ["☁️ CONTROL PLANE (Gerenciado pela Databricks)"]
        A["🖥️ Web UI & Notebooks"]
        B["⚙️ Workflows & Jobs Orchestrator"]
        C["📜 Metastore / Control Logs"]
    end

    subgraph ComputePlane ["🛡️ COMPUTE PLANE / DATA PLANE (Conta da Nuvem do Cliente)"]
        subgraph AWS ["Amazon Web Services"]
            D1["💻 EC2 / EKS Clusters"]
            D2["🗄️ Amazon S3 (Delta Lake)"]
            D3["🔑 AWS IAM / KMS"]
        end

        subgraph Azure ["Microsoft Azure"]
            E1["💻 Azure VMs / AKS"]
            E2["🗄️ ADLS Gen2 (Delta Lake)"]
            E3["🔑 Azure Entra ID / Key Vault"]
        end

        subgraph GCP ["Google Cloud Platform"]
            F1["💻 Compute Engine (GCE)"]
            F2["🗄️ Google Cloud Storage (GCS)"]
            F3["🔑 Service Accounts / Cloud KMS"]
        end
    end

    ControlPlane -->|Comandos de Orquestração / Metadados| AWS
    ControlPlane -->|Comandos de Orquestração / Metadados| Azure
    ControlPlane -->|Comandos de Orquestração / Metadados| GCP

    style ControlPlane fill:#1e293b,stroke:#38bdf8,color:#fff
    style AWS fill:#0f172a,stroke:#f59e0b,color:#fff
    style Azure fill:#0f172a,stroke:#3b82f6,color:#fff
    style GCP fill:#0f172a,stroke:#22c55e,color:#fff
