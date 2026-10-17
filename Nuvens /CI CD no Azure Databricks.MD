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


---

## 🛠️ Principais Componentes de uma Pipeline de CI/CD

### 1. Databricks Asset Bundles (DABs)
É a ferramenta padrão recomendada pelo Databricks para gerenciar projetos como código. Com os Bundles, você define:

Quais notebooks e arquivos .py pertencem ao projeto.

Como os Databricks Jobs devem ser configurados (clusters, agendamentos, parâmetros).

Os alvos de deploy (dev, staging, production).

###  2. O Arquivo de Pipeline (Exemplo de Azure DevOps YAML)
No Azure DevOps, a pipeline de CI/CD é definida em um arquivo chamado azure-pipelines.yml na raiz do seu repositório. Ela geralmente executa etapas como:

Instala a CLI do Databricks.

Autentica de forma segura usando tokens ou Service Principals.

Executa o comando de deploy (databricks bundle deploy -t production).

💡 Boas Práticas para Engenheiros de Dados
Nunca desenvolva direto em Produção: Sempre crie sua branch de trabalho (feature/...), teste em um workspace ou cluster de desenvolvimento, e suba via Pull Request.

Parametrize Credenciais: Nunca deixe senhas, chaves de API ou tokens hardcoded dentro dos notebooks. Utilize secrets do Databricks (dbutils.secrets.get) vinculados ao Azure Key Vault.

Modularização: Separe a lógica de negócio em arquivos .py testáveis, deixando nos notebooks apenas as chamadas principais de orquestração.
