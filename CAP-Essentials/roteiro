# 🧭 Bússola de Estudos: Microsoft Certified Fabric Data Engineer Associate (DP-700)

Este guia é um roadmap prático para a certificação **DP-700**, que substituiu a DP-203 (aposentada pela Microsoft em 31/03/2025). Ele segue os **3 domínios oficiais do guia de estudo da Microsoft** e começa com um módulo de fundamentos do Azure, que não cai na prova mas é base para o dia a dia.

> 📅 Matéria **Fabric e Azure** do plano de 12 meses: aula às **terças**, laboratório a cada 3 quintas e prova final no **início de outubro de 2027**.

---

## 📋 Sobre a prova

| Item | Detalhe |
| :--- | :--- |
| **Domínios** | 3 domínios, cada um com 30–35% da prova |
| **Nota mínima** | 700 (de 1000) |
| **Simulado oficial** | Practice assessment gratuito no Microsoft Learn |
| **Idioma** | Se a prova não estiver no seu idioma, é possível pedir 30 minutos extras |
| **Preço e duração** | Confira na página da prova no Microsoft Learn ao agendar |

### 🔗 O que você já sabe e que cai aqui

O Microsoft Fabric usa os mesmos conceitos do Databricks: **lakehouse, Delta Lake, Spark, Medallion e streaming**. A diferença está nos nomes dos produtos e na forma de configurar.

| No Databricks | No Fabric |
| :--- | :--- |
| Unity Catalog + storage de nuvem | **OneLake** (um único data lake para a organização) |
| Workspace | Workspace (ligado a uma **capacidade**) |
| Tabelas Delta | Tabelas Delta no **Lakehouse** |
| SQL Warehouse | **Warehouse** (T-SQL) e endpoint SQL do lakehouse |
| Lakeflow Jobs | **Pipelines** (Data Factory no Fabric) |
| Notebooks PySpark | Notebooks PySpark |
| Structured Streaming | **Eventstreams**, Spark Structured Streaming e **Eventhouse** (KQL) |
| Git folders + Bundles | **Integração com Git** + **deployment pipelines** |

---

## ☁️ Módulo 0: Fundamentos do Azure (nov/26 a jan/27, fora da prova)
Entender a nuvem onde rodam o Azure Databricks e os serviços de dados da Microsoft.

[ ] Organização do Azure: tenant, **assinaturas**, **grupos de recursos** e regiões.

[ ] Identidade: **Microsoft Entra ID** (usuários, grupos, service principals e managed identities) e controle de acesso com **RBAC**.

[ ] Armazenamento: **ADLS Gen2** (contas de armazenamento, containers, hierarquia de pastas), níveis de acesso e permissões.

[ ] Segurança e rede básica: Key Vault para segredos, private endpoints e firewall em alto nível.

[ ] **Azure Data Factory**: pipelines, atividades de cópia, linked services e integration runtime.

[ ] **Azure Databricks**: como o Databricks se integra ao Azure (Entra ID, ADLS Gen2 via Access Connector, Key Vault).

📎 *Revisar: Fundamentals Módulo 1 (arquitetura multi-cloud) e a nota `Nuvens /CI CD no Azure Databricks.md`.*

---

## 🏗️ Módulo 1: Implement and manage an analytics solution (30–35%)
Configurar, proteger, versionar e orquestrar uma solução no Fabric.

### Fundamentos do Fabric (fev/27)

[ ] O que é o Fabric: plataforma SaaS, **capacidades** (SKUs F), workspaces e **OneLake**.

[ ] Quando usar cada armazenamento: **Lakehouse** (Spark, arquivos + tabelas Delta), **Warehouse** (T-SQL, transações) e **Eventhouse** (KQL, tempo real).

[ ] Configurações do workspace: **Spark** (pools e ambientes), **domínios**, **OneLake** e fluxos de dados.

### Segurança e governança (mar/27)

[ ] Acesso por **workspace**: papéis Admin, Member, Contributor e Viewer.

[ ] Acesso por **item** (compartilhar um lakehouse ou warehouse específico).

[ ] Segurança em **nível de linha, coluna, objeto e arquivo**.

[ ] **Mascaramento dinâmico de dados** (*dynamic data masking*).

[ ] **Rótulos de confidencialidade** (*sensitivity labels*) e **endosso** de itens (*promoted* e *certified*).

### Ciclo de vida (abr/27)

[ ] **Controle de versão** com integração Git.

[ ] **Database projects** para o Warehouse.

[ ] **Deployment pipelines** (desenvolvimento → teste → produção).

### Orquestração (mai/27)

[ ] Quando usar um **pipeline** e quando usar um **notebook**.

[ ] **Agendamentos** e **gatilhos por evento**.

[ ] Padrões de orquestração com pipelines e notebooks: **parâmetros** e **expressões dinâmicas**.

---

## 📥 Módulo 2: Ingest and transform data (30–35%)
O domínio mais prático. Muito do que você aprendeu no Databricks se aplica aqui.

### Padrões de carga (mai/27)

[ ] Cargas **completas** e **incrementais**.

[ ] Preparar dados para um **modelo dimensional** (fatos e dimensões).

[ ] Padrão de carga para **dados em streaming**.

### Ingestão e transformação em lote (mai e jun/27)

[ ] Escolher o armazenamento adequado (lakehouse, warehouse ou eventhouse).

[ ] Escolher entre **Dataflow Gen2**, **notebooks** e **T-SQL** para transformar dados.

[ ] **Shortcuts**: acessar dados de outros lugares (ADLS, S3, outro lakehouse) sem copiar.

[ ] **Mirroring**: replicar bancos de dados para o OneLake quase em tempo real.

[ ] Ingerir dados com **pipelines** (atividade Copy).

[ ] Transformar com **PySpark, SQL e KQL**.

[ ] **Desnormalizar**, **agrupar** e **agregar** dados.

[ ] Tratar dados **duplicados, ausentes e que chegam atrasados** (*late-arriving data*).

### Ingestão e transformação em streaming (jul/27)

[ ] Escolher o motor de streaming adequado.

[ ] Processar dados com **eventstreams**.

[ ] Processar dados com **Spark Structured Streaming**.

[ ] Processar dados com **KQL**.

[ ] **Funções de janela** (*windowing*) em streaming: tumbling, hopping, sliding e session.

---

## 🔍 Módulo 3: Monitor and optimize an analytics solution (30–35%) · ago/27
Diagnosticar e otimizar, como no Módulo 6 da Data Engineer Associate.

### Monitoramento

[ ] Monitorar ingestão, transformação e atualização de **modelos semânticos**.

[ ] Configurar **alertas** (Monitoring hub e Activator).

### Identificar e resolver erros

[ ] Erros de **pipelines**, **dataflows** e **notebooks**.

[ ] Erros de **eventhouse** e **eventstream**.

[ ] Erros de **T-SQL**.

### Otimizar desempenho

[ ] Otimizar tabelas do **lakehouse** (OPTIMIZE, V-Order, VACUUM).

[ ] Otimizar **pipelines** e o **warehouse**.

[ ] Otimizar **eventstreams** e **eventhouses**.

[ ] Otimizar o desempenho do **Spark** e das **consultas**.

---

## ⚠️ Possíveis pegadinhas

| ❌ Confusão comum | ✅ O correto |
| :--- | :--- |
| "Shortcut copia os dados para o OneLake." | Shortcut só **aponta** para os dados; **mirroring** replica; o **Copy** de um pipeline copia. |
| "Lakehouse e Warehouse são a mesma coisa." | Lakehouse é para Spark e arquivos (o SQL é só leitura); Warehouse aceita T-SQL completo, inclusive escrita. |
| "KQL serve para qualquer carga." | KQL e Eventhouse são para dados de **tempo real** e séries temporais. |
| "Dataflow Gen2 é sempre a melhor opção para transformar." | Dataflow Gen2 é **low-code**; para lógica complexa ou grande volume, use notebooks; para quem domina SQL, T-SQL no Warehouse. |
| "Integração Git e deployment pipelines fazem a mesma coisa." | Git **versiona** o código; deployment pipelines **promovem** itens entre ambientes. |
| "Viewer pode editar itens do workspace." | Viewer só lê; editar exige **Contributor** ou acima. |

---

## 🧪 Prática recomendada

[ ] Criar um ambiente com **trial do Fabric** (confira a disponibilidade na sua conta).

[ ] Recriar a camada Bronze e Silver do **Trama Store** em um lakehouse do Fabric.

[ ] Simular vendas em tempo real com um **eventstream** e consultar com KQL.

[ ] Fazer o **practice assessment oficial** pelo menos duas vezes (meta: acima de 80%).

---

## 📚 Recursos oficiais

- [Guia de estudo da DP-700 (Microsoft Learn)](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700)
- [Trilhas de aprendizagem e cursos da DP-700](https://learn.microsoft.com/en-us/credentials/certifications/exams/dp-700#two-ways-to-prepare)
- [Practice assessment oficial](http://aka.ms/DP700-PA)
- [Vídeo Exam Prep da DP-700](https://aka.ms/DP700-ExamPrep)
- [Documentação do Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/)
- [Engenharia de dados no Fabric](https://learn.microsoft.com/en-us/fabric/data-engineering/data-engineering-overview)

---

## 💡 Dica de Ouro para a Prova
A DP-700 cobra muito **"qual ferramenta usar em cada situação"**: pipeline ou notebook, Dataflow ou T-SQL, lakehouse, warehouse ou eventhouse, shortcut ou mirroring. Monte uma tabela de decisão no seu caderno, e use a técnica de **intercalação**: misture essas escolhas nos flashcards de sexta-feira junto com as do Databricks.

---

### 🔗 Fontes
- [Guia de estudo DP-700 — Microsoft Learn](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700)
- [Aposentadoria da DP-203 — Vlad Talks Tech](https://vladtalkstech.com/azure/microsoft-retires-the-dp-203-azure-data-engineer-associate-certification/)

*Consultado em 04/11/2026.*
