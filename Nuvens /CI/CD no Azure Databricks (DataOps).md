# ⚙️ CI/CD no Azure Databricks (DataOps)

Este documento detalha como implementar práticas de **Integração Contínua (CI)** e **Entrega Contínua (CD)** em projetos de Engenharia de Dados usando o **Azure Databricks** e o **Azure DevOps**.

---

## 🎯 Por que aplicar CI/CD em Dados?

O CI/CD elimina o modelo manual de "arrastar arquivos para dentro do workspace do Databricks". As principais vantagens são:
* **Prevenção de Erros em Produção:** Nenhum código vai para o ambiente produtivo sem passar por revisões (*Pull Requests*) e testes automatizados.
* **Reprodutibilidade:** O ambiente de produção é um espelho exato do repositório de código (Git).
* **Agilidade nas Entregas:** Atualizar jobs, rotinas PySpark ou pipelines de ingestão passa a ser feito com um único clique ou *merge*.

---

## 🔄 O Ciclo de Vida do CI/CD com Databricks e Azure DevOps

```mermaid
flowchart LR
    A[Desenvolvedor escreve código] -->|Push para branch feature| B[Azure Repos / GitHub]
    B -->|Abre Pull Request| C[Azure Pipelines: CI <br> Testes & Linting]
    C -->|Aprovado e Merge| D[Azure Pipelines: CD <br> Deploy Automático no Databricks]
    D -->|Atualiza Workspace & Jobs| E[(Databricks Produção)]
