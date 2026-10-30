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
