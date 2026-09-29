# Apache Spark & PySpark

Este repositório reúne os conceitos fundamentais, arquitetura e exemplos práticos de uso do **Apache Spark** e de sua interface em Python, o **PySpark**, ferramentas essenciais no ecossistema de Engenharia e Ciência de Dados de grande escala (Big Data).

---

##  O que é o Apache Spark?

O **Apache Spark** é um motor de processamento distribuído de código aberto projetado para processar, consultar e analisar volumes massivos de dados (**Big Data**) em velocidade extremamente alta.

Enquanto ferramentas tradicionais rodam em uma única máquina (limitadas pela memória RAM e capacidade de processamento do computador), o Spark distribui o trabalho entre centenas ou milhares de servidores (**clusters**).

---

## 🐍 O que é o PySpark?

**PySpark** é a API em **Python** para o Apache Spark. Ela combina a facilidade de uso, a popularidade e a vasta gama de bibliotecas da linguagem Python com a potência de processamento distribuído do motor Spark por trás dos panos.

---

## ⚙️ Como o Spark Funciona por Trás dos Panos?

1. **Arquitetura Master-Worker (Driver e Executors):**
   * **Driver Program:** É o processo principal que gerencia o fluxo de trabalho, traduz o seu código em um plano lógico e coordena as tarefas.
   * **Executors:** São os nós de trabalho no cluster que executam as tarefas em paralelo e armazenam os dados em memória.
2. **Avaliação Preguiçosa (*Lazy Evaluation*):**
   * O Spark não processa os dados imediatamente quando você escreve uma linha de código. Ele cria um plano de execução otimizado (através do *Catalyst Optimizer*). A computação real só ocorre quando uma **Ação** é chamada.

---

## 📊 Conceitos Fundamentais

* **SparkSession:** O ponto de entrada oficial para programar com o Spark. Inicializa a conexão com o motor de processamento.
* **DataFrames:** Tabelas distribuídas em formato de linhas e colunas (semelhantes aos DataFrames do Pandas, mas capazes de escalar horizontalmente para petabytes de dados).

---

## 💻 Exemplo Prático de Código PySpark

```python
from pyspark.sql import SparkSession

# 1. Inicializando a sessão do Spark
spark = SparkSession.builder.appName("ExemploPipelinePySpark").getOrCreate()

# 2. Lendo um dataset em formato Parquet de um Data Lake (ex: Amazon S3)
df = spark.read.parquet("s3a://meu-datalake/vendas_brutas/")

# 3. Transformações: Filtrando dados e agrupando por categoria
df_transformado = (
    df.filter(df["status"] == "concluido")
    .groupBy("categoria")
    .sum("valor_total")
    .withColumnRenamed("sum(valor_total)", "faturamento_total")
)

# 4. Ação: Exibindo o resultado no console
df_transformado.show()
