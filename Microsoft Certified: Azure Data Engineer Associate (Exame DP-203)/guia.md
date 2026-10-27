# Bússola de Estudos: Microsoft Certified: Azure Data Engineer Associate (DP-203)

Bem-vindo(a) ao seu guia definitivo de estudos para a certificação DP-203. Este repositório serve como sua bússola para cobrir todos os domínios exigidos pela Microsoft, estruturado em tópicos práticos e teóricos.

📊 Pesquisa de Domínios do Exame DP-203

O exame é dividido em três grandes pilares de engenharia de dados na nuvem Azure:

Projetar e Desenvolver Processos de Dados (40–45%)

Implementar e Gerenciar Soluções de Armazenamento de Dados (20–25%)

Desenvolver e Otimizar o Processamento de Dados (25–30%)

📝 Checklist de Estudos (Sua Trilha Prática)

1. Projetar e Desenvolver Processos de Dados (ETL / ELT & Orquestração)

[ ] Azure Data Factory (ADF) & Azure Synapse Pipelines:

[ ] Criação de Pipelines, Linked Services e Datasets.

[ ] Uso de atividades de controle de fluxo (If Condition, For-Each, Switch, Until).

[ ] Tratamento de erros e reexecuções (Retry, Activities on Failure).

[ ] Parâmetros e Variáveis de sistema.

[ ] Integration Runtimes (IR):

[ ] Diferença e aplicação entre Azure IR, Self-Hosted IR e SSIS IR.

[ ] Ingestão e Transformação de Dados:

[ ] Mapeamento de dados com Mapping Data Flows (transformações visuais sem código).

[ ] Execução de notebooks e scripts via Databricks ou Spark dentro do ADF.

[ ] Ingestão incremental de dados (captura de alterações - CDC / Watermarking).

2. Implementar e Gerenciar Soluções de Armazenamento de Dados

[ ] Azure Data Lake Storage Gen2 (ADLS Gen2):

[ ] Arquitetura baseada em pastas (Landing/Bronze, Silver, Gold).

[ ] Segurança e Controle de Acesso Baseado em Funções (RBAC) e ACLs hierárquicas.

[ ] Azure Synapse Analytics:

[ ] Arquitetura de Dedicated SQL Pools vs. Serverless SQL Pools.

[ ] Modelagem de dados para data warehouses (Tabelas fato e dimensão, estratégias de distribuição: Hash, Round Robin, Replicated).

[ ] Otimização de índices e particionamento.

[ ] NoSQL e Relacionais Operacionais:

[ ] Fundamentos do Azure Cosmos DB (níveis de consistência e partições).

[ ] Operações básicas em bancos relacionais gerenciados (Azure SQL Database).

3. Desenvolver e Otimizar o Processamento de Dados (Spark / Databricks)

[ ] Conceitos de Apache Spark no Azure (Databricks / Synapse Spark):

[ ] Arquitetura de clusters (Driver e Workers).

[ ] Manipulação de DataFrames (Leitura, escrita e transformações em PySpark/Scala).

[ ] Otimização de performance: Shuffle, Partitioning, Broadcast Joins e Caching.

[ ] Delta Lake:

[ ] Vantagens do formato Delta (transações ACID, Time Travel, otimização de arquivos com OPTIMIZE e VACUUM).

4. Segurança, Governança, Monitoramento e Sustentabilidade de Dados

[ ] Segurança e Credenciais:

[ ] Integração com Azure Key Vault para armazenamento seguro de chaves, tokens e strings de conexão.

[ ] Mascaramento de dados e criptografia em repouso e trânsito.

[ ] Monitoramento e Otimização Operacional (Sustentabilidade):

[ ] Monitoramento de execuções no ADF e Azure Databricks.

[ ] Configuração de alertas e métricas com Azure Monitor e Log Analytics.

[ ] Análise de custos e otimização de recursos de computação.

💡 Dicas de Ouro para a Prova

Foco em Cenários: A Microsoft adora perguntas baseadas em cenários práticos (ex: "Qual ferramenta utilizar para ingestão de 50 fontes diferentes com transformação visual e baixo custo?" -> ADF Data Flows).

Integração ADF + Databricks: Entenda muito bem quando usar o Data Flow nativo do ADF versus quando disparar um notebook do Databricks/Spark.

Simulados Práticos: Reserve as últimas semanas antes da prova para resolver simulados focados no exame DP-203 para se acostumar com o estilo de cobrança da banca.

Bons estudos, Camila! Este arquivo é vivo: vá marcando os itens ([x]) conforme avançar na sua jornada rumo à certificação!
