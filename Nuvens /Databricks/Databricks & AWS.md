#  Integração Databricks & AWS: Arquitetura, Segurança e Prática

Este repositório documenta como o **Databricks** se conecta nativamente com o ecossistema da **AWS (Amazon Web Services)**, formando uma arquitetura robusta de *Lakehouse* sem perder o controle de segurança e infraestrutura da sua conta em nuvem.

---

##  1. O Conceito Central: Onde cada plataforma roda?

O Databricks **não possui infraestrutura própria de servidores físicos**. Ele opera de forma integrada dividindo sua arquitetura em dois grandes planos:

### Control Plane (Plano de Controle)
* **O que é:** É a camada gerenciada diretamente pelo Databricks.
* **O que contém:** A interface web, o gerenciador de notebooks, o agendador de trabalhos (*Jobs*), os metadados de gerenciamento e o ecossistema de workspace.
* **Onde fica:** Na nuvem gerenciada pela Databricks.

###  Compute Plane (Plano de Computação)
* **O que é:** É onde o processamento pesado de fato acontece (os clusters do Spark, leitura de arquivos e transformações).
* **Onde fica:** **Dentro da sua própria conta da AWS**, isolado na sua **VPC (Virtual Private Cloud)**. 
* **Vantagem de Engenharia:** Os dados e o processamento pesado nunca saem do perímetro de segurança da sua empresa. O Databricks apenas envia comandos e recebe os resultados de visualização.

---

##  2. Armazenamento: A Base no Amazon S3

O coração do armazenamento no Databricks dentro da AWS é o **Amazon S3**.

* **O S3 como Single Source of Truth:** Todos os dados do seu Data Lake (camadas Bronze, Silver e Gold em formato Parquet ou Delta Lake) ficam armazenados fisicamente em **Buckets do S3** na sua conta AWS.
* **Sem Vendor Lock-in:** Como os dados estão em arquivos abertos no seu S3, você pode acessá-los a qualquer momento usando outras ferramentas da AWS (como Athena ou Glue), mesmo sem abrir a interface do Databricks.

---

##  3. Segurança, Permissões e Governança (IAM & Unity Catalog)

A governança entre as duas plataformas é feita através de credenciais seguras e camadas unificadas de metadados:

* **AWS IAM (Identity and Access Management):** O acesso do Databricks aos seus buckets do S3 é regulado por **IAM Roles** e **Instance Profiles** (perfis de instância). O Databricks assume uma função segura na AWS para ler e gravar dados apenas nas pastas permitidas.
* **Unity Catalog vs. AWS Glue Data Catalog:** 
  * O Databricks utiliza o **Unity Catalog** para governança refinada (controle de acesso a nível de tabelas, linhas e colunas).
  * É possível sincronizar o Unity Catalog com o **AWS Glue Data Catalog** para que ferramentas nativas da AWS enxerguem as tabelas criadas no Databricks.

---

## 4. Computação, Performance e Custos (EC2 & Graviton)

Como os clusters rodam na sua conta AWS, você tem total controle sobre os custos e os tipos de hardware utilizados:

* **Instâncias Amazon EC2:** Quando você inicia um cluster no Databricks, ele automaticamente solicita instâncias de servidores virtuais (EC2) na sua conta AWS e as destrói quando o trabalho acaba (otimização de custos).
* **Suporte a AWS Graviton:** O Databricks suporta processadores baseados em arquitetura ARM da AWS (**Graviton2 / Graviton3**). O uso de instâncias Graviton reduz drasticamente o custo por hora de processamento do Spark com excelente performance.

---

##  5. Como o Databricks se integra com outras ferramentas AWS

No dia a dia de engenharia de dados, o Databricks atua no centro da arquitetura conectando-se com outros serviços:

| Serviço AWS | Função na Integração com Databricks |
| :--- | :--- |
| **Amazon S3** | Armazenamento de objetos (Camada de dados do Lakehouse). |
| **AWS Glue** | Descoberta de esquemas (*Crawlers*) e catálogo complementar de metadados. |
| **Amazon Athena** | Consultas SQL rápidas diretamente sobre os arquivos Delta Lake gerados pelo Databricks no S3. |
| **Amazon Redshift** | Destino final (*Gold*) para cargas consolidadas que alimentam relatórios corporativos de BI. |
| **AWS Lambda** | Pode ser acionado por eventos do Databricks para disparar alertas ou pipelines complementares. |

---

##  Fluxo Arquitetural da Integração

```mermaid
flowchart TD
    subgraph Conta AWS do Cliente
        A[Amazon S3 <br> Data Lake / Buckets] 
        B[AWS VPC <br> Clusters EC2 / Compute Plane]
        C[IAM Roles <br> Segurança & Permissões]
    end

    subgraph Databricks Cloud
        D[Control Plane <br> Workspace & Notebooks]
    end

    D -->|Envia comandos Spark| B
    B -->|Lê e Grava dados com segurança| A
    C -->|Autoriza acesso restrito| A
    B -.->|Utiliza instâncias da| C
