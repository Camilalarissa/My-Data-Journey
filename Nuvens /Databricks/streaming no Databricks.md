# streaming no Databricks

O objetivo principal do streaming no Databricks (implementado principalmente via Structured Streaming) é processar e analisar volumes contínuos de dados em tempo real ou quase real (near real-time),
eliminando a necessidade de aguardar por janelas de processamento em lote (batch).

## Principais Objetivos e Benefícios

- Tomada de Decisão em Tempo Real:
   Permite que sistemas e equipes reajam instantaneamente a eventos do negócio, como detecção de fraudes financeiras,monitoramento de sensores IoT, personalização de recomendações em e-commerce e alertas de rastreamento logístico.

- Ingestão Incremental e Eficiente:
Através de recursos como o Auto Loader (cloudFiles), o Databricks identifica e processa automaticamente apenas os arquivos ou eventos novos que chegam ao Data Lake, reduzindo drasticamente o consumo de memória e CPU.

- Modelo de Tabela Sem Fim (Unbounded Table):
Abstrai o fluxo contínuo de dados como uma tabela que cresce infinitamente. Isso permite reaproveitar a mesma lógica de código em PySpark ou SQL utilizada em cargas batch tradicionais.

- Tolerância a Falhas e Garantia Exactly-Once:
Utiliza mecanismos de Checkpointing e logs de transação do Delta Lake para garantir que, se um cluster cair, o pipeline retome do exato ponto em que parou, sem duplicar registros ou perder dados.

- Unificação de Arquitetura (Batch + Streaming): ]
Substitui a antiga "Arquitetura Lambda" (que exigia pipelines separados para tempo real e lote). No Lakehouse, a mesma tabela Delta pode ser alimentada via streaming e consultada em lote simultaneamente sem conflitos de leitura/escrita.
