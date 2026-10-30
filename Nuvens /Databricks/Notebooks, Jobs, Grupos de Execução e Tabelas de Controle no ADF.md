# 📑 Guia Técnico: Notebooks, Jobs, Grupos de Execução e Tabelas de Controle no ADF

Este documento explica os pilares de processamento no Databricks (**Notebooks**, **Jobs** e **Grupos de Execução**) e a estrutura das **Tabelas de Controle e Auditoria** utilizadas pelo **Azure Data Factory (ADF)** para orquestração dinâmica e cargas incrementais.

---

## 📓 1. Notebook (Ambiente Interativo de Desenvolvimento)

Um **Notebook** é uma interface baseada em web que combina código executável, documentação em texto formatado (Markdown), consultas SQL e visualizações gráficas de dados em um único documento interativo.

### ⚙️ Funcionalidades e Papel no Dia a Dia
* **Desenvolvimento e Prototipagem:** É o ambiente primário onde o Engenheiro de Dados constrói a lógica de transformação de dados (Camadas Silver e Gold) usando PySpark, SQL, Scala ou Python.
* **Conexão com Clusters:** Um notebook precisa estar anexado (*attached*) a um cluster ativo para enviar comandos e receber respostas em tempo real.
* **Poliglota no Nível de Célula:** Permite alternar linguagens na mesma sessão usando Comandos Mágicos (`%python`, `%sql`, `%md`, `%sh`).
* **Modularidade:** Pode importar outros notebooks via `%run` ou chamar funções de arquivos Python (`.py`) versionados via Git Folders.

---

## ⚙️ 2. Job (Processamento Automatizado e Não Interativo)

Um **Job** (ou *Databricks Workflow Task*) é a unidade de execução automatizada, agendada ou orquestrada de um ou mais notebooks/scripts sem a necessidade de intervenção humana.

### ⚙️ Funcionalidades e Papel no Dia a Dia
* **Produção 24/7:** Enquanto o Notebook é usado para *desenvolvimento*, o **Job** é o formato oficial para rodar pipelines em *produção*.
* **Orientado a DAGs (Directed Acyclic Graphs):** Um Job pode encadear dezenas de tarefas com dependências complexas (ex: *Rodar Notebook A e B em paralelo; se ambos terminarem com sucesso, rodar Notebook C*).
* **Execução em Job Clusters:** Por padrão, um Job cria um cluster temporário (*Job Cluster*) exclusivo para a execução e o destrói imediatamente ao final, reduzindo os custos de computação em até 50%.
* **Alertas e Notificações:** Envia e-mails ou gatilhos para Webhooks (Teams/Slack) automaticamente em caso de falhas ou estouro de SLA.

---

## 🏊 3. Grupo de Execução (Instance Pools & Concorrência)

O termo **Grupo de Execução** refere-se a dois conceitos fundamentais de infraestrutura e gerenciamento de recursos em arquiteturas de dados:

### A. Instance Pools (Grupos de Instâncias no Databricks)
Um **Pool** é um conjunto de instâncias de máquinas virtuais (VMs) mantidas em estado "pronto" (*idle/warm*) na sua conta de nuvem (Azure/AWS).
* **Problema que resolve:** A criação de um novo cluster do zero leva de 3 a 6 minutos para alocar máquinas na nuvem.
* **Como funciona:** O Pool mantém máquinas pré-alocadas. Quando um Job dispara, ele pega essas VMs do Pool e inicia o cluster em **menos de 40 segundos**.
* **Redução de Custo:** Quando o Job termina, as máquinas voltam para o Pool em vez de serem terminadas e recontratadas.

### B. Concurrency Groups / Filas de Execução (No ADF e Databricks)
Define o limite máximo de pipelines ou tarefas que podem rodar simultaneamente (*paralelismo*).
* Evita que 100 pipelines tentem acessar a mesma fonte de dados (ex: um banco de dados relacional transacional) ao mesmo tempo, prevenindo gargalos de CPU e quedas por limite de conexões.

---

## 🗃️ 4. Estruturas das Tabelas Ligadas ao ADF (Arquitetura Meta-Driven)

Para construir pipelines de alta maturidade no Azure Data Factory, não criamos um pipeline para cada tabela do banco de origem. Em vez disso, construímos **Pipelines Dinâmicos (Meta-Driven)** que utilizam **três estruturas de tabelas de controle e governança**:



┌────────────────────────┐      ┌────────────────────────┐      ┌────────────────────────┐
│ 1. Tabela de Controle  │ ───► │  2. Tabela Watermark   │ ───► │  3. Tabela de Auditoria│
│   (O que ingerir?)     │      │ (Onde o pipeline parou?)│     │   (Status e Métricas)  │
└────────────────────────┘      └────────────────────────┘      └────────────────────────┘

---

### 📋 A. Tabela de Controle de Ingestão (`control_metadata`)
Armazena a lista cadastral de todas as tabelas das fontes de dados que o ADF deve buscar e jogar na Camada Bronze.

```sql
CREATE TABLE config.control_metadata (
    id_pipeline INT IDENTITY(1,1) PRIMARY KEY,
    system_source VARCHAR(50),       -- Ex: 'POSTGRES_ERP', 'INTELIPOST_API'
    source_schema VARCHAR(50),       -- Ex: 'public'
    source_table VARCHAR(100),      -- Ex: 'vendas'
    target_path VARCHAR(255),       -- Ex: 'bronze/erp/vendas/'
    file_format VARCHAR(20),        -- Ex: 'parquet', 'json'
    loading_type VARCHAR(20),       -- Ex: 'FULL', 'INCREMENTAL'
    is_active BIT DEFAULT 1         -- 1 = Ativo, 0 = Pausado
);


---

CREATE TABLE config.control_watermark (
    source_table VARCHAR(100) PRIMARY KEY,
    watermark_column VARCHAR(50),   -- Ex: 'updated_at', 'id_transacao'
    last_watermark_value VARCHAR(100), -- Ex: '2026-10-30 08:00:00'
    updated_at DATETIME DEFAULT GETDATE()
);

---

## 🔄 Fluxo do ADF com a Tabela Watermark:
O ADF lê o last_watermark_value atual da tabela de controle (ex: 2026-10-29 00:00:00).

Busca no banco de origem: SELECT * FROM vendas WHERE updated_at > '2026-10-29 00:00:00'.

Salva os novos dados na Bronze.

Atualiza o last_watermark_value na tabela de controle com o maior valor encontrado na carga atual.

## 📜 C. Tabela de Log e Auditoria (audit_pipeline_execution)
Registra o histórico de execução de cada execução do ADF para observabilidade, acompanhamento de volume e diagnósticos de erros.

SQL

CREATE TABLE config.audit_pipeline_execution (
    execution_id UNIQUEIDENTIFIER PRIMARY KEY, -- @pipeline().RunId do ADF
    pipeline_name VARCHAR(100),                 -- @pipeline().Pipeline
    trigger_type VARCHAR(50),                  -- Ex: 'ScheduleTrigger', 'Manual'
    source_table VARCHAR(100),
    status VARCHAR(20),                         -- 'RUNNING', 'SUCCESS', 'FAILED'
    rows_read BIGINT,                           -- Registros lidos da origem
    rows_written BIGINT,                        -- Registros salvos na Bronze
    start_time DATETIME,                        -- @pipeline().TriggerTime
    end_time DATETIME,
    error_message VARCHAR(MAX) NULL
);


## 📊 Tabela Comparativa de Conceitos

| Conceito | Tipo / Categoria | Onde Roda? | Função Principal |
| :--- | :--- | :--- | :--- |
| **Notebook** | Ambiente de Desenvolvimento | Cluster Interativo do Databricks | Criação, testes e transformações de código (PySpark/SQL). |
| **Job** | Orquestrador e Agendador | Workflows do Databricks (Job Cluster) | Automação e execução autônoma de pipelines em produção. |
| **Instance Pool** | Gestão de Infraestrutura | Nuvem (Azure VMs / AWS EC2) | Reduzir o tempo de inicialização de clusters de 5 min para < 1 min. |
| **Tabela de Controle** | Estrutura de Dados SQL | Banco SQL / Metadata Store do ADF | Parametrizar dinamicamente o que o ADF deve ingerir. |
| **Tabela Watermark** | Estrutura de Dados SQL | Banco SQL / Metadata Store do ADF | Rastrear o ponto de parada de cargas incrementais. |

