# 🗂️ Databricks Workspace: Navegação, Organização e Controle de Acesso (ACLs)

> **Exame:** Databricks Lakehouse Fundamentals  
> **Tópico:** Módulo 1 — Navegação pela Interface, Gerenciamento de Pastas, Workspaces e ACLs Básicos.

---

## 📐 1. Hierarquia de Objetos do Workspace e Governança (Mermaid.js)

O diagrama abaixo ilustra como a estrutura organizacional do **Databricks Workspace** separa os ativos de código/arquivos dos recursos de computação e como as permissões de acesso (**ACLs**) são aplicadas:

```mermaid

graph TD
    subgraph WS ["DATABRICKS WORKSPACE (Tenant / Ambiente)"]
        subgraph Folders ["Diretórios e Pastas (/Workspace)"]
            U["/Users/seu_email (Privado por padrão)"]
            S["/Shared (Acessível a todos os usuários)"]
            G["/Repos ou Git Folders (Sincronizado via Git)"]
        end

        subgraph Objects ["Objetos do Workspace"]
            N1["Notebooks (.py, .sql, .ipynb)"]
            F1["Subpastas"]
            E1["Experimentos MLflow"]
            SC["Escopos Secretos (Cofres de Segredos)"]
        end

        subgraph Compute ["Recursos de Computação"]
            C1["Clusters Interativos"]
            J1["Tarefas / Fluxos de Trabalho"]
            W1["Armazéns SQL"]
        end
    end

    subgraph Governance ["Controle de Acesso (ACLs & Roles)"]
        Admin["Administrador do Espaço de Trabalho"]
        User["Engenheiro/Analista de Dados"]
        Group["Grupos de Usuários (ex: Equipe de Ciência de Dados)"]
    end

    Governance -->|Gerencia Permissões| Folders
    Governance -->|Define Permissões| Objects
    Governance -->|Atribui Acesso| Compute

    Folders --> Objects
```
---
## 🧭 2. Navegação na Interface (Databricks UI Personas)

A barra lateral esquerda (*Left Navigation Bar*) permite alternar a experiência do usuário de acordo com o seu perfil de trabalho (**Personas**):

| Módulo / Persona | Ícone / Seção | Funcionalidade e Ativos Principais |
| :--- | :--- | :--- |
| **Workspace (Geral)** | 📂 **Workspace** | Navegação na árvore de arquivos, notebooks, pastas e repositórios Git. |
| **Data Engineering** | ⚙️ **Workflows / Compute** | Criação e monitoramento de Clusters Spark, agendamento de Jobs e Pipelines de DLT (Delta Live Tables). |
| **Databricks SQL** | 🧱 **SQL Warehouses / Queries** | Criação de dashboards visuais, editor de consultas SQL ad-hoc, alertas e gestão de SQL Warehouses. |
| **Machine Learning** | 🧪 **Experiments / Models** | Rastreamento de experimentos com MLflow, Feature Store e registro de modelos para deploy. |
| **Data Governance** | 🗄️ **Catalog (Unity Catalog)** | Exploração de Schemas, Tabelas Delta, Volumes, Linhagem de Dados e Permissões globais. |


## 📁 3. Estrutura e Gerenciamento de Pastas

| Caminho do Diretório | Escopo de Acesso Padrão | Uso Indicado no Dia a Dia |
| :--- | :--- | :--- |
| `/Workspace/Users/<email>/` | **Privado** ao usuário proprietário. | Prototipagem, rascunhos de código e análises exploratórias individuais. |
| `/Workspace/Shared/` | **Público** para todos os usuários do Workspace. | Templates de notebooks corporativos, documentações gerais e pipelines compartilhados. |
| `/Workspace/Users/<email>/<git-repo>` *(Git Folders)* | **Isolado por Usuário** com sincronização remota. | Desenvolvimento oficial com branches Git (GitHub/Azure Repos) voltado a CI/CD. |
   

