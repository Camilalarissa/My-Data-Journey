# 🌊 Apache Airflow: Orquestração de Fluxos de Dados

Este documento reúne os conceitos fundamentais, a arquitetura e a importância do **Apache Airflow**, a ferramenta de código aberto mais popular do mundo para orquestração de pipelines e fluxos de trabalho de dados.

---

## 🎯 O que é o Apache Airflow?

O **Airflow** funciona como um **maestro** (orquestrador). Se a sua empresa possui dezenas ou centenas de scripts (em Python, SQL, chamadas de APIs) que precisam rodar em uma **ordem específica**, em horários programados e com tratamento automático de erros, o Airflow é a ferramenta que coordena toda essa engrenagem.

---

## 🏛️ O Conceito Central: O que é uma DAG?

No Airflow, qualquer pipeline de dados é programado em Python utilizando o conceito de **DAG** (*Directed Acyclic Graph* ou Grafo Acíclico Direto).

* **Direto (Directed):** As tarefas seguem um fluxo com direção lógica clara (ex: Passo A $\to$ Passo B $\to$ Passo C).
* **Acíclico (Acyclic):** Significa que o fluxo **nunca entra em loop infinito** (o último passo nunca pode retornar ciclicamente para reiniciar o primeiro sem controle).
* **Tasks (Tarefas):** Cada etapa individual dentro da DAG (ex: *"Baixar arquivo do S3"*, *"Executar script de transformação"*, *"Carregar no Data Warehouse"*).

---

## ⚙️ Principais Funcionalidades e Vantagens

1. **Pipelines como Código (*Configuration as Code*):** 
   As DAGs são escritas puramente em **Python**. Isso permite utilizar controle de versão (Git/GitHub), testes automatizados e toda a flexibilidade da linguagem de programação.
2. **Interface Visual Robusta (UI):** 
   O Airflow oferece uma interface web intuitiva para monitorar em tempo real o status das execuções (sucesso, falha, tempo de execução de cada tarefa e o fluxo gráfico completo).
3. **Gestão de Dependências Avançadas:** 
   Permite definir regras complexas de execução (ex: *"A Tarefa 3 só roda se a Tarefa 1 E a Tarefa 2 terminarem com sucesso"*).
4. **Resiliência e Retentativas (*Retries*):** 
   Se uma API instável falhar momentaneamente durante a ingestão, o Airflow pode ser configurado para tentar novamente de forma automática após alguns minutos.
5. **Ecossistema de Integrações (*Operators/Providers*):** 
   Possui conectores nativos para interagir com a nuvem (AWS S3, Lambda, EMR), bancos de dados relacionais, Snowflake, Databricks e ferramentas de BI.

---

## ⚖️ Apache Airflow vs. Databricks Jobs: Quando usar qual?

Embora ambos orquestrem tarefas, eles possuem escopos diferentes em uma arquitetura de dados:

* **Databricks Jobs:** Ideal quando o seu pipeline de dados roda **inteiramente dentro do ecossistema Databricks/Spark**.
* **Apache Airflow:** É uma ferramenta **agnóstica e centralizadora**. É utilizada quando o fluxo de dados envolve múltiplos sistemas heterogêneos (ex: *Extrai de uma API externa $\to$ Salva no Amazon S3 $\to$ Aciona um Job no Databricks $\to$ Executa uma rotina no banco relacional $\to$ Envia alerta no Slack*). 

> *Nota de Arquitetura:* Em empresas de grande porte, é muito comum usar o **Airflow como o orquestrador macro** da empresa, que inclusive possui operadores nativos para acionar e disparar os **Databricks Jobs** na hora certa.
