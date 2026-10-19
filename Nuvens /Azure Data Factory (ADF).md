# 📘 Guia Completo: Azure Data Factory (ADF) de A a Z
O Azure Data Factory (ADF) é o serviço de integração de dados em nuvem (Platform as a Service - PaaS) totalmente gerenciado e serverless da Microsoft Azure.

Ele é projetado para resolver a complexidade de conectar dezenas de sistemas heterogêneos (on-premise, multicloud e SaaS), mover dados em grande escala e orquestrar fluxos complexos de ETL (Extract, Transform, Load) e ELT (Extract, Load, Transform).

## 🏛️ 1. O Conceito Central: O que o ADF faz (e o que NÃO faz)
Para utilizar o ADF como um Engenheiro de Dados sênior, é essencial entender o seu papel na arquitetura:

O que o ADF É:

Um Orquestrador Macro: O "maestro" do Data Lakehouse.

Um Motor de Movimentação de Dados: Excelente para extrair dados de origens (APIs, bancos relacionais, servidores SFTP, SharePoint) e salvar no Data Lake (ADLS Gen2).

Um Agendador de Tarefas: Gerencia dependências, retentativas (retries) e notificações.

O que o ADF NÃO É:

Um motor de banco de dados: Ele não armazena dados persistentes.

Ideal para transformações pesadas via código puro: Embora possua o Mapping Data Flows, transformações complexas de Big Data (limpeza, cruzamentos massivos) geralmente são delegadas para o Azure Databricks ou Spark.

## ⚙️ 2. Arquitetura e Componentes Fundamentais
O ADF é construído sobre 6 blocos estruturais de montagem:

---
[ Trigger ] ➔ [ Pipeline ] ➔ [ Activity ] ➔ [ Dataset ] ➔ [ Linked Service ] ➔ (Fonte/Destino)
                                 ↓
                       [ Integration Runtime ]

---

## 1️⃣ Linked Services (Serviços Vinculados)
São as "strings de conexão" com segurança reforçada.

Definem o endereço IP, porta, método de autenticação e credenciais do recurso externo.

Exemplos: Conexão com um SQL Server, Azure Blob Storage, REST API, Amazon S3, Salesforce ou Azure Key Vault.

## 2️⃣ Datasets (Conjuntos de Dados)
Representam a estrutura lógica dos dados dentro de um Linked Service.

Apontam para pastas, tabelas, arquivos específicos ou esquemas.

Exemplos: Um arquivo .csv em uma pasta específica do ADLS Gen2, uma tabela vendas_2026 dentro de um banco PostgreSQL, ou uma coleção no MongoDB.

## 3️⃣ Activities (Atividades)
São os passos de execução individuais dentro de um pipeline. Dividem-se em três categorias:

Movimentação de Dados: Copy Data Activity (A atividade mais usada no ADF para ler da origem e gravar no destino com alto paralelismo).

Transformação de Dados: Execução de Notebooks do Databricks, procedimentos armazenados (Stored Procedures), scripts em Python, pacotes SSIS ou Mapping Data Flows.

Controle de Fluxo: If Condition, For Each, Until, Switch, Set Variable, Execute Pipeline, Lookup (para buscar metadados) e Get Metadata.

## 4️⃣ Pipelines (Pipelines)
O fluxo de trabalho (Workflow) propriamente dito. Agrupa uma ou mais Atividades dispostas em sequência, paralelo ou condicional, executando uma regra de negócio ponta a ponta.

## 5️⃣ Integration Runtime (IR) - O Motor de Execução
É a infraestrutura computacional que fornece a capacidade de rede e memória para rodar as atividades. Existem 3 tipos:

### ☁️ Azure IR: Infraestrutura 100% gerenciada pela Microsoft na nuvem. Usada para mover dados entre fontes públicas de nuvem (ex: de um S3 para o ADLS Gen2).

 ### 🏠 Self-Hosted IR (SHIR): Um software instalado em uma máquina virtual interna (on-premise) ou dentro de uma VNet privada da empresa. É a ponte de segurança que permite ao ADF acessar um banco de dados interno da empresa sem expô-lo para a internet.

### 📦 Azure-SSIS IR: Um cluster dedicado para rodar pacotes legados do SQL Server Integration Services (SSIS) na nuvem sem precisar reescrevê-los.

### 6️⃣ Triggers (Gatilhos)
Definem quando ou como um pipeline deve ser executado:

Schedule Trigger: Agendamento cronológico tradicional (ex: rodar todo dia às 02:00 AM).

Tumbling Window Trigger: Janelas temporais periódicas retendo o estado histórico (ideal para reprocessamentos por intervalo de tempo).

Storage Event Trigger: Disparado por eventos de infraestrutura (ex: rodar assim que um novo arquivo .csv cair no container da camada Bronze).

Custom Event Trigger: Disparado via Event Grid através de eventos externos de aplicações.

## 🎨 3. Mapping Data Flows (Transformação Visual Low-Code)
O Mapping Data Flows é o motor de transformação visual sem código do ADF.

Como funciona: Você constrói graficamente fluxos de ETL (junções, agregações, pivôs, derivar colunas).

Por baixo dos panos: O ADF traduz suas caixas visuais em código otimizado e dispara um cluster temporário de Apache Spark (gerenciado automaticamente) para processar os dados.

Uso recomendado: Casos onde a equipe não domina PySpark/Scala e prefere manutenções visuais rápidas para tratamentos intermediários.

## 👷‍♀️ 4. O ADF no Dia a Dia do Engenheiro de Dados
No fluxo de trabalho diário de uma equipe de dados, o ADF cumpre papéis estratégicos bem definidos:

1. Ingestão Automatizada na Camada Bronze (Landing Zone)
Extrair dados de APIs REST paginadas, bancos transacionais (PostgreSQL/SQL Server) ou arquivos recebidos por SFTP/SharePoint.

Adicionar metadados automáticos (ex: DataIngestao, NomeArquivoOrigem) e salvar o arquivo bruto em formato Parquet ou JSON no Azure Data Lake Storage Gen2 (ADLS Gen2).

2. Orquestração do Databricks
O ADF copia os dados para a Camada Bronze.

Na sequência, chama a atividade Databricks Notebook Activity, passando parâmetros como data_processamento e id_execucao.

O Databricks faz o processamento pesado (gerando Silver e Gold) e devolve o status de sucesso para o ADF.

3. Tratamento de Erros e Observabilidade
Implementar blocos de erro (Upon Failure) para enviar alertas automáticos para canais do Microsoft Teams, Slack ou e-mails via Azure Logic Apps quando um pipeline quebrar.

4. Pipelines Parametrizados e Dinâmicos
Em vez de criar 100 pipelines para 100 tabelas diferentes, um Engenheiro de Dados cria 1 único pipeline genérico (Meta-driven Pipeline) que:

Lê uma tabela de controle com a lista de tabelas a serem ingeridas.

Usa um loop For Each para percorrer a lista.

Copia cada tabela dinamicamente usando expressões em linguagem ADF (@dataset().TableName).

## 🔐 5. Governança, Segurança e CI/CD (DataOps)

### 🔑 Azure Key Vault & Managed Identities
Zero senhas no código: As credenciais de bancos e tokens de APIs nunca ficam visíveis no ADF.

O ADF usa Identidade Gerenciada (Managed Identity) para se autenticar no Azure Key Vault e buscar as credenciais criptografadas dinamicamente no momento da execução.

🔀 Versionamento e CI/CD (Azure DevOps / GitHub)
O ADF é integrado nativamente ao Git (Azure Repos ou GitHub).

Todo o desenvolvimento visual é convertido automaticamente em arquivos JSON / ARM Templates (Azure Resource Manager).

A esteira de CI/CD no Azure DevOps pega esses arquivos JSON do ambiente de Desenvolvimento (Dev) e publica automaticamente nos ambientes de Teste (Homolog) e Produção (Prod), ajustando os parâmetros do Key Vault para cada ambiente.

## 📊 Comparativo de Mercado: Onde o ADF se Posiciona?

| Critério / Recurso | 🟦 Azure Data Factory (ADF) | 🧱 Databricks Workflows | 🌊 Apache Airflow |
| :--- | :--- | :--- | :--- |
| **Interface & Usabilidade** | Low-Code / No-Code visual (*Drag-and-Drop* na Web). | Interface visual e código via YAML (*Databricks Asset Bundles*). | *Configuration as Code* (100% código Python em DAGs). |
| **Foco Principal** | Ingestão, movimentação de dados e orquestração de nuvem. | Orquestração focada no ecossistema Spark, Delta Lake e MLOps. | Orquestração macro centralizada de sistemas heterogêneos. |
| **Infraestrutura** | 100% *Serverless* / *PaaS* gerenciado pela Microsoft. | *Serverless* ou computação gerenciada em nuvem (AWS/Azure/GCP). | Requer gerenciamento de infraestrutura (Docker / Kubernetes / VMs). |
| **Poder de Transformação** | Baixo a Médio (*Copy Activity* / *Mapping Data Flows*). | Altíssimo (Execução nativa em clusters PySpark, SQL e Photon). | Médio a Alto (Delega o processamento pesado para motores externos). |
| **Flexibilidade Agnóstica** | Alta no ecossistema Azure; médio suporte a outras nuvens. | Focado em cargas de trabalho dentro ou conectadas ao Databricks. | Totalmente agnóstico (conecta qualquer API, banco ou nuvem). |
| **Integração com Azure** | Nativa e perfeita (*Key Vault*, *ADLS Gen2*, *Entra ID*, *Synapse*). | Nativa via *Azure Databricks*. | Requer configuração manual de conectores e bibliotecas. |
| **Paradigma Principal** | Declarativo / Visual via arquivos JSON (*ARM Templates*). | Declarativo e imperativo (Python / SQL / YAML). | Imperativo via código Python. |
| **Curva de Aprendizado** | Baixa a Média (Ideal para times com perfil SQL/Analítico). | Média (Exige conhecimento em Spark e Databricks). | Média a Alta (Exige boas práticas de desenvolvimento em Python e DevOps). |



# Azure Data Factory (ADF) - Cheatsheet

* **Função:** Orquestrador de ETL/ELT e movimentação de dados em nuvem.
* **Pilares:** Linked Services (Conexão) ➔ Datasets (Estrutura) ➔ Activities (Ações) ➔ Pipelines (Fluxo) ➔ Triggers (Agendamento).
* **Motor:** Integration Runtime (Azure IR para Nuvem, Self-Hosted IR para On-Premise).
* **Boas Práticas:** 
  1. Use pipelines parametrizados (Meta-Driven) para evitar código duplicado.
  2. Guarde todas as credenciais no **Azure Key Vault**.
  3. Delegue transformações pesadas para o **Azure Databricks** via Databricks Activity.
  4. Integre com **Azure Repos** e **Azure DevOps** para automação de CI/CD via ARM Templates.

