# 🧭 Bússola de Estudos: Databricks Certified Data Engineer Associate

Este guia é um roadmap prático para a certificação **Databricks Certified Data Engineer Associate**, organizado pelos **7 domínios do guia oficial do exame (versão de 4 de maio de 2026)**. Ele parte da base já construída na **Databricks Fundamentals Accreditation** (concluída em 01/11/2026).

---

## 📋 Sobre a prova

| Item | Detalhe |
| :--- | :--- |
| **Formato** | 45 questões de múltipla escolha (pode haver questões extras não pontuadas) |
| **Duração** | 90 minutos |
| **Custo** | US$ 200 + impostos |
| **Aplicação** | Online (com supervisão) ou em centro de testes |
| **Consulta** | Nenhum material de apoio permitido |
| **Pré-requisitos** | Nenhum obrigatório; recomenda-se curso + cerca de 6 meses de prática com Databricks |
| **Validade** | 2 anos (recertificação refazendo a prova) |
| **Nota de corte** | Não divulgada oficialmente (estimativas da comunidade: ~70%) |

### ⚖️ Peso aproximado dos domínios

> ⚠️ A Databricks **não publica** os percentuais. Os valores abaixo são estimativas da comunidade, baseadas na quantidade de objetivos de cada domínio.

| Domínio | Peso estimado | Base que você já tem |
| :--- | :--- | :--- |
| 1. Databricks Intelligence Platform | ~10% | ✅ Fundamentals Módulos 1, 4, 5 e 6 |
| 2. Data Ingestion and Loading | ~20% | 🆕 Quase tudo novo |
| 3. Data Transformation and Modeling | ~25% | 🟡 Fundamentals Módulos 3 e 4 (aprofundar) |
| 4. Working with Lakeflow Jobs | ~15% | 🟡 Fundamentals Módulo 5 (aprofundar) |
| 5. Implementing CI/CD | ~10% | 🟡 Nota `Git Folders.md` + 🆕 Bundles e CLI |
| 6. Troubleshooting, Monitoring and Optimization | ~10% | 🆕 Spark UI e diagnóstico |
| 7. Governance and Security | ~10% | 🟡 Fundamentals Módulo 5 (aprofundar) |

> 💡 **Onde focar:** os domínios 2 e 3 somam quase metade da prova e são os que mais exigem prática.

---

## 🏗️ Módulo 1: Databricks Intelligence Platform (~10%)
Revisar a arquitetura e saber **escolher o compute certo** para cada carga.

[ ] Componentes centrais: arquitetura (Control Plane, Classic e Serverless Compute Plane), Delta Lake e Unity Catalog.

[ ] Serviços de compute: all-purpose, job compute, serverless e SQL Warehouses. Seus limites, modelos de custo e **como escolher o compute ideal** para cada carga de trabalho.

📎 *Revisar: Fundamentals Módulos 1 (Arquitetura e Clusters), 4 e 5.*

---

## 📥 Módulo 2: Data Ingestion and Loading (~20%)
Dominar **todas as formas de colocar dados no Lakehouse** e saber quando usar cada uma.

[ ] Tipos de ingestão: **batch, streaming e incremental**. Importar arquivos locais e usar os conectores padrão e gerenciados do **Lakeflow Connect**.

[ ] **COPY INTO**: carregar de forma incremental arquivos do cloud storage em tabelas do Unity Catalog (idempotente: não recarrega arquivos já processados).

[ ] **Auto Loader** (`cloudFiles`): modos *directory listing* vs. *file notification*, com **schema enforcement e schema evolution**.

[ ] **Lakeflow Connect**: configurar ingestão a partir de fontes corporativas (bancos de dados, SaaS).

[ ] Ingestão via notebook com clientes **JDBC/ODBC** ou **REST**, orquestrada por Lakeflow Jobs.

[ ] **Escolher o método certo** (Auto Loader, Lakeflow Connect, conectores de parceiros e outros) com base em volume, frequência, tipo de dado e necessidades de governança.

[ ] Ingerir dados **semiestruturados e não estruturados** (JSON, dados aninhados) em tabelas Delta.

💡 *Pegadinha clássica: COPY INTO vs. Auto Loader. COPY INTO é mais simples, para milhares de arquivos; Auto Loader escala para milhões de arquivos e suporta streaming e evolução de schema.*

---

## 🔄 Módulo 3: Data Transformation and Modeling (~25%)
O domínio de **maior peso**. Exige prática real com PySpark e SQL.

[ ] Limpar dados da **Bronze** com PySpark ou SQL (tratar nulos, converter tipos) e gravar tabelas **Silver**.

[ ] **Joins**: inner, left, **broadcast**, com múltiplas chaves, cross, `union` e `union all`.

[ ] Manipular colunas e linhas: adicionar, remover, dividir (`split`) e renomear colunas, filtrar linhas e **explodir arrays** (`explode`).

[ ] **Deduplicação** e agregações: `count`, contagem distinta aproximada (`approx_count_distinct`), média e `summary`/`describe`.

[ ] **Ajuste básico do Spark**: `spark.sql.shuffle.partitions`, paralelismo padrão, memória do executor/driver e `autoBroadcastJoinThreshold`. Medir a performance antes e depois.

[ ] Diferenciar e construir objetos **Gold**: **materialized views**, **views**, **streaming tables** e **tabelas**.

[ ] Aplicar **regras de qualidade e validação** (*expectations*) em datasets Silver e Gold.

[ ] **Lakeflow Spark Declarative Pipelines** (antigo Delta Live Tables): pipelines declarativos com streaming tables, materialized views e expectations.

📎 *Revisar: Fundamentals Módulos 3 (Spark) e 4 (Delta e Medallion).*

---

## ⚙️ Módulo 4: Working with Lakeflow Jobs (~15%)
Ir além do "agendar um notebook": **controle de fluxo** e **escolha de gatilhos**.

[ ] **Controle de fluxo**: retries, **ramificação condicional** (*if/else task*) e **loops** (*for each task*).

[ ] Configurar tasks de **notebook, SQL, dashboard e pipeline** e suas dependências em um **DAG**.

[ ] Gatilhos: **agendado (cron)**, **chegada de arquivo (file arrival)** e **atualização de tabela (table update)**.

[ ] **Escolher entre gatilho por tempo e gatilho por dados**, com base na disponibilidade dos dados e nas dependências.

💡 *Lembre do treino da Fundamentals: arquivo chegando → file arrival; tabela atualizada → table update; horário fixo → cron.*

📎 *Revisar: Fundamentals Módulo 5.*

---

## 🚀 Módulo 5: Implementing CI/CD (~10%)
Levar código do desenvolvimento para produção de forma **automatizada e versionada**.

[ ] Gerenciar código pela interface do workspace com **Git folders**: branches, commits, push e **pull requests**.

[ ] Configurações por ambiente (**dev, test, prod**) com **variáveis e overrides** nos Bundles.

[ ] **Declarative Automation Bundles** (antigos *Databricks Asset Bundles*, DABs): implantar jobs, pipelines e outros ativos do workspace.

[ ] **Databricks CLI**: validar (`bundle validate`), implantar (`bundle deploy`) e gerenciar bundles em fluxos de CI/CD.

📎 *Revisar: nota `Nuvens /Databricks/Git Folders.md` e `Nuvens /CI CD no Azure Databricks.md`.*

---

## 🔍 Módulo 6: Troubleshooting, Monitoring and Optimization (~10%)
Saber **diagnosticar** quando algo está lento ou falha.

[ ] Comparar o tempo das execuções atuais com o **histórico de execuções** (baseline).

[ ] Monitorar a saúde dos pipelines: status dos jobs, visão do DAG, tempos de execução e taxa de falhas.

[ ] Identificar **data skew**, **shuffle** e **disk spilling** pelas métricas de *stage* na **Spark UI**.

[ ] Entender **Liquid Clustering** e **predictive optimization**.

[ ] Diagnosticar **falhas de inicialização de cluster**, **conflitos de bibliotecas** e erros de **falta de memória (OOM)**.

📎 *Revisar: Fundamentals Módulo 3 (narrow vs. wide e shuffle) e Módulo 4 (OPTIMIZE e Liquid Clustering).*

---

## 🛡️ Módulo 7: Governance and Security (~10%)
Aprofundar o Unity Catalog para o nível **prático**.

[ ] **Tabelas gerenciadas vs. externas**: criar, modificar, apagar e **converter** entre os dois tipos.

[ ] Aplicar **GRANT, REVOKE e DENY** a usuários, grupos e **service principals**, no nível correto, pela interface ou por SQL.

[ ] **Mascaramento de colunas** e **segurança em nível de linha** com base nos grupos do usuário.

[ ] **Políticas ABAC** (*attribute-based access control*) do Unity Catalog para filtros de linha e máscaras de coluna centralizados.

📎 *Revisar: Fundamentals Módulo 5.*

---

## 📅 Plano sugerido de 6 semanas

| Semana | Foco | Entrega no repositório |
| :--- | :--- | :--- |
| 1 | Módulo 1 (revisão) + Módulo 7 (governança) | Notas dos Módulos 1 e 7 |
| 2 | Módulo 2: COPY INTO, Auto Loader, Lakeflow Connect | Nota do Módulo 2 + notebook de ingestão |
| 3 | Módulo 3: joins, limpeza, agregações, tuning | Nota do Módulo 3 + notebook Bronze → Silver |
| 4 | Módulo 3: Gold, Declarative Pipelines, expectations | Pipeline Medallion completo |
| 5 | Módulos 4 e 5: Lakeflow Jobs, Bundles e CLI | Job orquestrado + bundle com dev/prod |
| 6 | Módulo 6 + simulados | Nota do Módulo 6 + 2 simulados acima de 80% |

---

## 📚 Treinamentos oficiais recomendados

**Instrutor:** Data Engineering with Databricks

**Self-paced (Databricks Academy):**

[ ] Data Ingestion with Lakeflow Connect

[ ] Deploy Workloads with Lakeflow Jobs

[ ] Build Data Pipelines with Lakeflow Spark Declarative Pipelines

[ ] Data Management and Governance with Unity Catalog

[ ] DevOps Essentials for Data Engineering

[ ] Data Interoperability with Unity Catalog

---

## 💡 Dica de Ouro para a Prova:
Diferente da Fundamentals, a Associate é **prática**: muitas questões mostram **trechos de código PySpark ou SQL** e perguntam o resultado, o erro ou a melhor opção. Ler a teoria não basta: **pratique em um workspace** (por exemplo, no Databricks Free Edition) escrevendo ingestões, transformações, jobs e permissões de verdade.

E lembre da lição da Fundamentals: **a prova segue o curso oficial**. Faça os cursos recomendados da Academy antes da prova, porque nomes e conceitos novos aparecem lá primeiro.

---

### 🔗 Fontes
- [Guia oficial do exame — Databricks Certified Data Engineer Associate (4 de maio de 2026)](https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf)
- [Pesos estimados dos domínios — DriveDataScience](https://drivedatascience.com/databricks-certified-data-engineer-associate-study-guide-autoloader-lakeflow-unity-catalog/)

*Consultado em 01/11/2026.*
