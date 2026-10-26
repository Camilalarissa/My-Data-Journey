# ⚙️ CI/CD no Azure Databricks (DataOps)

Este documento detalha como implementar práticas de **Integração Contínua (CI)** e **Entrega Contínua (CD)** em projetos de Engenharia de Dados usando o **Azure Databricks** e o **Azure DevOps**.


Os Pilares do CI/CD no Databricks
Para implementar CI/CD com o Databricks utilizando ferramentas como o Azure DevOps (Azure Pipelines), dividimos o processo em duas frentes principais:

1. 🔄 Integração Contínua (CI - Continuous Integration)
Ocorre sempre que um engenheiro de dados desenvolve uma nova funcionalidade (ex: um script de ingestão Bronze) e abre um Pull Request (PR) para enviar o código à branch principal (main).

O que a CI faz automaticamente:

Validação de Sintaxe e Testes Unitários: O Azure Pipelines roda validações de código para garantir que não há erros de sintaxe em Python/PySpark.

Linting: Verifica se o código segue os padrões de estilo estabelecidos (ex: Flake8, Black).

Build de Artefatos: Compacta o código em pacotes ou assets prontos para deploy (ex: usando o Databricks Asset Bundles - DABs).

Entrega/Implantação Contínua (CD - Continuous Deployment)
Ocorre assim que o código é aprovado e integrado (merged) na branch de produção.

O que a CD faz automaticamente:

O Azure Pipelines se conecta via API ao workspace de produção do Databricks.

Deploy Automático: Os novos notebooks, arquivos Python ou configurações de Databricks Jobs são enviados automaticamente para a pasta correta no workspace produtivo.

Atualização de Workflows: Se o pipeline de ingestão Bronze foi alterado, o Azure DevOps atualiza automaticamente a definição do job no Databricks para refletir as novas regras.

Ferramentas Utilizadas na Prática
Controle de Versão (Git / Azure Repos): Onde todo o código do Databricks fica versionado.

Databricks Asset Bundles (DABs): A ferramenta oficial da Databricks focada em CI/CD. Ela permite definir a infraestrutura, jobs e arquivos de workspace em código (arquivos YAML), facilitando o deploy via linha de comando (databricks bundle deploy).

Azure Pipelines: O motor de automação do Azure DevOps que executa os passos de CI/CD toda vez que um commit ou merge é realizado.



---

## 🎯 Por que aplicar CI/CD em Dados?

O CI/CD elimina o modelo manual de "arrastar arquivos para dentro do workspace do Databricks". As principais vantagens são:
* **Prevenção de Erros em Produção:** Nenhum código vai para o ambiente produtivo sem passar por revisões (*Pull Requests*) e testes automatizados.
* **Reprodutibilidade:** O ambiente de produção é um espelho exato do repositório de código (Git).
* **Agilidade nas Entregas:** Atualizar jobs, rotinas PySpark ou pipelines de ingestão passa a ser feito com um único clique ou *merge*.

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
