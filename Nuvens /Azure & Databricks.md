# ☁️🔗 Integração Azure & Databricks: Arquitetura e Funcionamento

Este documento detalha como o **Azure Databricks** se integra de forma nativa e profunda com o ecossistema de nuvem da **Microsoft Azure**, unindo governança, segurança e processamento distribuído de alta performance.

---

## 🏛️ 1. O Conceito Central: Onde cada plataforma roda?

O Azure Databricks não é apenas um software instalado em um servidor; a própria Microsoft o gerencia como um serviço de primeira classe (*first-party service*). Sua arquitetura é dividida em dois planos principais:

### 🎛️ Control Plane (Plano de Controle)
* **O que é:** É a camada de gerenciamento e interface web.
* **Onde fica:** É gerenciada diretamente pela equipe do Databricks. É onde ficam a interface dos Notebooks, o agendamento de trabalhos e os metadados do workspace.

### ⚙️ Compute Plane (Plano de Computação)
* **O que é:** É onde o processamento pesado de fato acontece (os clusters do Apache Spark, leituras de arquivos e transformações).
* **Onde fica:** **Dentro da sua própria assinatura do Azure**, isolado em uma **VNet (Virtual Network)** gerenciada pela sua empresa. Os dados e o processamento pesado nunca saem do perímetro de segurança da sua conta Azure.

---

## 📦 2. Armazenamento Base: Azure Data Lake Storage (ADLS Gen2)

O coração do armazenamento no Azure Databricks é o **Azure Data Lake Storage Gen2 (ADLS Gen2)**.

* **O ADLS Gen2 como Single Source of Truth:** Todos os arquivos do seu Lakehouse (camadas Bronze, Silver e Gold em formato Delta Lake ou Parquet) ficam armazenados fisicamente em containers dentro de uma conta de armazenamento do Azure.
* **Segurança Hierárquica:** O ADLS Gen2 possui um namespace hierárquico (semelhante a pastas de um computador), o que acelera drasticamente a leitura de grandes volumes de dados pelo Spark e permite controle rígido de permissões de pastas.

---

## 🔐 3. Segurança e Identidade: Microsoft Entra ID

A autenticação e o controle de acesso são totalmente integrados à identidade corporativa da Microsoft:

* **Microsoft Entra ID (Antigo Azure Active Directory):** Os usuários fazem login no Databricks usando suas contas corporativas da Microsoft.
* **Managed Identities (Identidades Gerenciadas):** Para que o cluster do Databricks acesse o Data Lake (ADLS Gen2) com segurança, utilizamos *Managed Identities*. O Databricks assume uma identidade segura na nuvem, eliminando a necessidade de salvar senhas, chaves de acesso ou tokens de texto plano dentro dos códigos e notebooks.

---

## ⚡ 4. Computação Otimizada e Instâncias Azure

Como os clusters rodam na sua assinatura do Azure, você pode escolher diferentes tipos de máquinas virtuais (**Azure VMs**) dependendo da necessidade do pipeline:
* Instâncias otimizadas para memória (para processamento pesado em Spark).
* Instâncias otimizadas para computação ou com suporte a **GPUs** (essenciais para treinamentos avançados de Machine Learning e Inteligência Artificial).

---

## 🔄 5. Como o Databricks se integra com o ecossistema Azure

No dia a dia de engenharia de dados, o Azure Databricks atua no centro da arquitetura conectando-se de forma fluida com outras ferramentas da Microsoft:

| Serviço Azure | Função na Integração com o Databricks |
| :--- | :--- |
| **Azure Data Lake Storage (ADLS Gen2)** | O armazenamento principal dos dados (camadas Bronze, Silver e Gold). |
| **Azure Data Factory (ADF)** | O orquestrador que move dados de fontes externas e **dispara os Notebooks do Databricks** via API. |
| **Microsoft Power BI** | Conecta-se diretamente aos **SQL Warehouses** do Databricks para consumir as tabelas consolidadas da Camada Gold e gerar relatórios de BI. |
| **Azure DevOps** | Utilizado para versionar o código (Git) e automatizar o deploy contínuo (CI/CD) dos notebooks e pipelines. |
| **Azure Purview / Unity Catalog** | Ferramentas de governança para rastrear a linhagem e a qualidade dos dados em toda a empresa. |

---

## 🗺️ Fluxo Arquitetural Resumido

```mermaid
flowchart TD
    subgraph Assinatura Azure do Cliente
        A[Azure Data Lake Storage Gen2 <br> Camadas Bronze, Silver, Gold] 
        B[Azure VNet <br> Clusters Spark / Compute Plane]
        C[Microsoft Entra ID <br> Segurança & Identidades]
    end

    subgraph Databricks Cloud
        D[Control Plane <br> Workspace & Notebooks]
    end

    D -->|Envia comandos de processamento| B
    B -->|Lê e Grava dados de forma segura| A
    C -->|Autentica acessos via Managed Identities| A
