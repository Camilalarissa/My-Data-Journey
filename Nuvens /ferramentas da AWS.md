# Guia Prático: Serviços AWS 

##  1. Camada de Armazenamento & Catalogação 

###  Amazon S3 (Simple Storage Service)
* **O que é:** O serviço de armazenamento de objetos altamente escalável da AWS. É o **coração de qualquer Data Lake**.
* **Função no dia a dia:**
  * Armazenar arquivos brutos (CSV, JSON, Parquet), logs, imagens e artefatos de Machine Learning.
  * **Engenharia de Dados:** Organiza a arquitetura de camadas do Data Lake (*Raw/Bronze*, *Silver* e *Gold*).
  * **Ciência de Dados:** Consome dados tratados diretamente dessas camadas para análises e treinamentos.

### AWS Glue Data Catalog
* **O que é:** Um repositório central de metadados que funciona como o "índice" do Data Lake.
* **Função no dia a dia:**
  * **Engenharia de Dados:** Configura *Crawlers* para varrer o S3, descobrir esquemas e criar tabelas virtuais sem mover os arquivos.
  * **Ciência de Dados:** Consulta o catálogo para descobrir quais tabelas, campos e colunas estão disponíveis na empresa.

---

##  2. Ferramentas do Engenheiro de Dados

O foco da engenharia é **construir, automatizar e otimizar pipelines (ETL/ELT)**, mover dados com segurança e garantir alta performance de leitura.

###  AWS Glue
* **O que é:** Serviço *Serverless* gerenciado para tarefas de **ETL** (Extração, Transformação e Carga).
* **Função no dia a dia:** Executar scripts em **Python/PySpark** para limpar, padronizar e transformar grandes volumes de dados de forma automatizada.

###  Amazon Redshift
* **O que é:** O **Data Warehouse** em nuvem focado em consultas analíticas de alta performance.
* **Função no dia a dia:** Armazenar dados modelados (em esquemas como *Star Schema*) para alimentar dashboards e ferramentas de BI com respostas em milissegundos.

###  Amazon Kinesis
* **O que é:** Plataforma para processamento de **dados em tempo real (Streaming)**.
* **Função no dia a dia:** Ingerir e processar fluxos contínuos de dados (cliques em sites, transações financeiras, sensores IoT) quase instantaneamente.

### AWS Lambda
* **O que é:** Serviço de computação *Serverless* orientado a eventos.
* **Função no dia a dia:** Criar automações pontuais e gatilhos (ex.: disparar um script em Python de validação assim que um novo arquivo chega ao S3).

---

## 3. Ferramentas do Cientista de Dados

O foco da ciência é **explorar dados, responder a perguntas de negócios complexas, criar hipóteses e treinar modelos de Inteligência Artificial**.

### Amazon Athena
* **O que é:** Ferramenta de consultas interativas *Serverless* que utiliza SQL padrão.
* **Função no dia a dia:** Fazer consultas SQL diretamente nos arquivos armazenados no Amazon S3 **sem a necessidade de carregar os dados em um banco de dados**.

### Amazon SageMaker
* **O que é:** Plataforma completa para construção, treinamento e implantação de modelos de **Machine Learning / IA**.
* **Função no dia a dia:**
  * Fornece ambientes de desenvolvimento (Jupyter Notebooks).
  * Permite preparar dados e treinar algoritmos usando bibliotecas como Pandas, Scikit-Learn, PyTorch e TensorFlow.
  * Faz o *deploy* do modelo como uma API pronta para produção.

###  Amazon Bedrock
* **O que é:** Serviço totalmente gerenciado para integração com **Modelos de IA Generativa** via API.
* **Função no dia a dia:** Construir aplicações inteligentes (como busca semântica e sistemas RAG) utilizando os melhores LLMs do mercado sem precisar treinar modelos do zero.

---

## 4. Visualização e Regra de Negócio

### Amazon QuickSight
* **O que é:** A ferramenta de **Business Intelligence (BI)** da AWS.
* **Função no dia a dia:**
  * **Engenharia de Dados:** Conecta a ferramenta às bases do Redshift ou Athena para expor visões consolidadas.
  * **Ciência de Dados:** Cria visualizações interativas para comunicar resultados e previsões para a liderança.

---

##  Fluxo de Dados na Prática

```mermaid
flowchart LR
    A[Amazon S3 - Raw] --> B[AWS Glue - ETL]
    B --> C[Amazon Redshift / S3 Silver]
    C --> D[Amazon Athena / SageMaker]
    C --> E[Amazon QuickSight]
