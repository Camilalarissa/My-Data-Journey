# 🗂️ Databricks Workspace: Navegação, Organização e Controle de Acesso (ACLs)

> **Exame:** Databricks Lakehouse Fundamentals  
> **Tópico:** Módulo 1 — Navegação pela Interface, Gerenciamento de Pastas, Workspaces e ACLs Básicos.

---

## 📐 1. Hierarquia de Objetos do Workspace e Governança (Mermaid.js)

O diagrama abaixo ilustra como a estrutura organizacional do **Databricks Workspace** separa os ativos de código/arquivos dos recursos de computação e como as permissões de acesso (**ACLs**) são aplicadas:

```mermaid
graph TD
    subgraph WS ["🧱 DATABRICKS WORKSPACE (Tenant / Ambiente)"]
        subgraph Folders ["📂 Diretórios e Pastas (/Workspace)"]
            U ["👤 /Users/seu_email (Privado por padrão)"]
            S ["👥 /Shared (Acessível a todos os usuários)"]
            G ["🌿 /Repos ou Git Folders (Sincronizado via Git)"]
        end

        subgraph Objects ["📄 Objetos do Workspace"]
            N1["📓 Notebooks (.py, .sql, .ipynb)"]
            F1["📁 Subpastas"]
            E1["🧪 MLflow Experiments"]
            SC["🔑 Secret Scopes (Cofres de Segredos)"]
        end

        subgraph Compute ["⚡ Recursos de Computação"]
            C1["🖥️ Interactive Clusters"]
            J1["⚙️ Jobs / Workflows"]
            W1["🧱 SQL Warehouses"]
        end
    end

    subgraph Governance ["🛡️ Controle de Acesso (ACLs & Roles)"]
        Admin["👑 Workspace Admin"]
        User["👨‍💻 Data Engineer / Analyst"]
        Group["👥 User Groups (ex: Data-Science-Team)"]
    end

    Governance -->|Gerencia Permissões| Folders
    Governance -->|Define Permissões| Objects
    Governance -->|Atribui Acesso| Compute

    Folders --> Objects

    style WS fill:#0f172a,stroke:#38bdf8,color:#fff
    style Folders fill:#1e293b,stroke:#94a3b8,color:#fff
```
## 🧭 2. Navegação na Interface (Databricks UI Personas)

A barra lateral esquerda (*Left Navigation Bar*) permite alternar a experiência do usuário de acordo com o seu perfil de trabalho (**Personas**):

| Módulo / Persona | Ícone / Seção | Funcionalidade e Ativos Principais |
| :--- | :--- | :--- |
| **Workspace (Geral)** | 📂 **Workspace** | Navegação na árvore de arquivos, notebooks, pastas e repositórios Git. |
| **Data Engineering** | ⚙️ **Workflows / Compute** | Criação e monitoramento de Clusters Spark, agendamento de Jobs e Pipelines de DLT (Delta Live Tables). |
| **Databricks SQL** | 🧱 **SQL Warehouses / Queries** | Criação de dashboards visuais, editor de consultas SQL ad-hoc, alertas e gestão de SQL Warehouses. |
| **Machine Learning** | 🧪 **Experiments / Models** | Rastreamento de experimentos com MLflow, Feature Store e registro de modelos para deploy. |
| **Data Governance** | 🗄️ **Catalog (Unity Catalog)** | Exploração de Schemas, Tabelas Delta, Volumes, Linhagem de Dados e Permissões globais. |
    style Objects fill:#334155,stroke:#cbd5e1,color:#fff
    style Compute fill:#1e293b,stroke:#f59e0b,color:#fff
    style Governance fill:#0f172a,stroke:#22c55e,color:#fff

