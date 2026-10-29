
# Log na Arquitetura Medalhão

No contexto de Engenharia de Dados e arquitetura Lakehouse, o conceito de Log na Arquitetura Medalhão aborda duas frentes complementares: o Log de Transações do Delta Lake (_delta_log) que sustenta as tabelas das camadas Bronze, Silver e Gold, e as tabelas/colunas de log e auditoria (DataOps) utilizadas para rastreabilidade de pipelines.

## 1. O Log de Transações do Delta Lake (_delta_log)
Toda tabela criada no formato Delta Lake em qualquer uma das camadas da Arquitetura Medalhão (Bronze, Silver ou Gold) contém uma pasta oculta chamada _delta_log no diretório de armazenamento (ADLS Gen2 ou S3).

/mnt/datalake/bronze/status_receipt/
├── _delta_log/
│   ├── 00000000000000000000.json
│   ├── 00000000000000000001.json
│   └── 00000000000000000010.checkpoint.parquet
├── part-00000-c000.snappy.parquet
└── part-00001-c000.snappy.parquet

### Como Funciona:

- Protocolo de Commit Criptográfico: Cada operação de escrita (INSERT, UPDATE, DELETE, MERGE) gera um novo arquivo JSON ordenado sequencialmente (000000.json, 000001.json).

- Checkpoints: A cada 10 commits, o Delta gera um arquivo .parquet de checkpoint para compactar o histórico e acelerar a leitura do estado atual da tabela.

- Garantias do Delta Log:

- Transações ACID: Garante que ou a carga é concluída 100%, ou nada é gravado (evita dados parciais/corrompidos).

- Time Travel (Viagem no Tempo): Permite consultar versões passadas da tabela lendo logs anteriores.

- Controle de Concorrência: Impede que dois pipelines sobrescrevam o mesmo arquivo ao mesmo tempo.

##  2. A Camada de Log de Ingestão e Auditoria (DataOps)
Trata-se de uma estratégia de observabilidade e rastreabilidade, onde os pipelines gravam metadados operacionais em tabelas especializadas ou diretamente nas tabelas da camada Bronze.

- A. Metadados de Log por Registro (Colunas Técnicas)
- Ao ingerir dados brutos na Camada Bronze, adicionam-se colunas técnicas de controle de log:

SELECT
  body,
  _input_file_name AS source_file,      -- Nome do arquivo de origem
  current_timestamp() AS load_timestamp, -- Data/hora exata do processamento
  current_date() AS load_date            -- Partição da carga
FROM bronze.status_receipt;


### B. Tabelas de Log da Pipeline (Orquestração)

Tabelas dedicadas (geralmente salvas em um schema audit ou logs) que registram o histórico de execução de orquestradores (Azure Data Factory / Databricks Workflows):

- pipeline_name: Nome da pipeline executada.

- execution_id: GUID / ID único da execução.

- status: SUCCESS, FAILED, RUNNING.

- rows_read / rows_written: Quantidade de linhas processadas.

- error_message: Mensagem detalhada em caso de falha.

## 3. O Conceito de "Append-Only Log" na Camada Bronze

Na Arquitetura Medalhão, a Camada Bronze atua como um Log Imutável de Eventos:

- Os dados chegam das fontes e são gravados no modo Append Only (apenas inserção).

- Nenhuma linha existente é atualizada (UPDATE) ou apagada (DELETE) na Bronze.

- Se um registro sofre alterações na origem (ex: um pedido muda de status de "EM TRÂNSITO" para "ENTREGUE"), a Bronze guarda as duas linhas com seus respectivos timestamps de carga. A deduplicação e a consolidação do estado final ocorrem posteriormente na Camada Silver.

📊 Comparativo: Delta Log vs. Log de Ingestão
