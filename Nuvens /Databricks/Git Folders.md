# 📂 Git Folders em Engenharia de Dados (DataOps)

**Git Folders** (anteriormente conhecidos como **Databricks Repos**) são diretórios integrados nativamente a provedores de controle de versão (como GitHub, Azure Repos, GitLab e Bitbucket) dentro de plataformas de Big Data, permitindo o gerenciamento de todo o código-fonte de pipelines sob as práticas de **DataOps**.

---

## ⚙️ Arquitetura e Funcionamento

Em vez de armazenar notebooks soltos e isolados no workspace, a **Git Folder** espelha a estrutura física e a árvore de arquivos de um repositório Git remoto.



[ Provedor Git Remoto (GitHub / Azure Repos) ]
                 ▲
                 │ Sincronização Bi-direcional (Fetch / Push / Pull)
                 ▼
 [ Workspace do Databricks / Data Platform ]
    └── Git Folder: /Users/seu_usuario/pipeline-vendas/
        ├── 📂 src/
        │   ├── 📄 ingestao_bronze.py
        │   └── 📄 transform_silver.py
        ├── 📂 config/
        │   └── 📄 pipeline_config.yaml
        ├── 📄 notebook_orquestrador.ipynb
        └── 📄 README.md

---

## 🧰 Principais Recursos

### 1. 🔐 Conexão e Autenticação Segura
* **Personal Access Token (PAT) / OAuth:** A integração é autenticada por usuário através de tokens seguros vinculados ao provedor de Git.
* **Isolamento de Ambiente:** Cada desenvolvedor clona seu repositório na pasta pessoal (`/Workspace/Users/seu_email/`), trabalhando de forma independente.

### 2. 📦 Suporte a Projetos Modulares
Permite organizar o repositório como um projeto de software profissional, aceitando múltiplos formatos de arquivo:
* **Scripts Python (`.py`):** Módulos, funções utilitárias e classes de transformação.
* **Scripts SQL (`.sql`):** Consultas complexas e comandos DDL/DML.
* **Arquivos de Configuração (`.yaml`, `.json`):** Mapeamento de parâmetros e tabelas.
* **Notebooks (`.ipynb`):** Orquestração e análises exploratórias.
* **Documentação (`.md`):** Manuais e instruções do repositório.

### 3. 🐍 Importação Nativa de Módulos (`sys.path`)
O diretório raiz da Git Folder é adicionado automaticamente ao caminho do sistema Python (`sys.path`) no cluster ativo. Isso permite importar funções customizadas sem a necessidade de empacotar bibliotecas adicionais:

python

# Importação direta do módulo src/limpeza.py
from src.limpeza import remover_duplicatas

df_limpo = remover_duplicatas(df_bronze)


4. 🔄 Interface Integrada para Operações Git
Permite executar todas as operações do Git sem sair da plataforma de dados:

Checkout de Branch: Alternar e criar ramificações (feature/ingestao-intelipost).

Visualização de Diff: Comparar alterações de código linha a linha.

Commit & Push: Salvar estados locais e enviar alterações para o servidor remoto.

Pull & Merge: Sincronizar atualizações da branch principal (main).

🎯 Por que Utilizar em Engenharia de Dados?
Rastreabilidade e Auditoria: Histórico completo de alterações contendo o autor, a data e a justificativa das mudanças no código.

Automação de CI/CD: Habilita esteiras automatizadas (GitHub Actions, Azure DevOps, Databricks Asset Bundles) para promover pipelines entre os ambientes de DEV, HOMOLOG e PROD.

Eliminação de Código Duplicado e Conflitos: Impede que desenvolvedores sobrescrevam o trabalho uns dos outros em um único notebook compartilhado.


## 📊 Comparativo: Workspaces Tradicionais vs. Git Folders

| Critério | ⚠️ Workspace Tradicional (Sem Git) | 🚀 Git Folders (DataOps) |
| :--- | :--- | :--- |
| **Versionamento** | Inexistente ou via histórico visual simplificado. | Controle completo via repositório Git (Commits/Branches). |
| **Estrutura de Arquivos** | Suporta apenas Notebooks soltos. | Projetos completos com `.py`, `.sql`, `.yaml` e `.md`. |
| **Reuso de Código** | Requer `%run` ou criação de pacotes `.whl`. | Importação direta de módulos (`from src.modulo import ...`). |
| **Deploy em Produção** | Alterações manuais e de alto risco. | Automação via CI/CD e Pull Requests aprovados. |
| **Colaboração** | Risco de conflito e sobrescrita de arquivo. | Trabalho isolado por branches com merge formal. |
