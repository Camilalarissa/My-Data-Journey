## Cloud Data Pipeline para E-commerce
Data: Junho de 2026
Objetivo do Projeto: Criar um pipeline de dados ponta a ponta (End-to-End), saindo da extração local (Web Scraping) para o armazenamento em nuvem e finalizando com um dashboard de Business Intelligence.

Ferramentas Utilizadas
Linguagem: Python

Bibliotecas: requests, BeautifulSoup (Scraping) e google-cloud-bigquery (Conexão Cloud)

Cloud & Storage: Google Cloud Platform (GCP), Service Accounts, Google BigQuery

Visualização (BI): Looker Studio

Ambiente & Versionamento: VS Code, uv (gerenciador de pacotes), Git e GitHub.

 O Processo que Segui
Configuração da Nuvem: Criei um projeto do zero no GCP, ativei a API do BigQuery e gerei uma chave de Conta de Serviço (credenciais.json) para que o meu código pudesse se autenticar com segurança.

Desenvolvimento do Script: Escrevi a lógica de extração focada em capturar títulos, preços e disponibilidade de produtos em uma loja virtual (Books to Scrape).

Carga no BigQuery: Programei o Python para criar o Dataset e a Tabela automaticamente (caso não existissem) e enviar os dados raspados diretamente para o banco em nuvem via API.

Visualização: Conectei o BigQuery ao Looker Studio e criei um painel interativo para exibir métricas como "Preço Médio" e distribuição de estoque.

Erros que Enfrentei e Como os Resolvi
Erro 1: O Python não achava as credenciais da nuvem

O que aconteceu: FileNotFoundError: [Errno 2] No such file or directory: 'credenciais.json'

A Solução: Percebi que o arquivo JSON baixado do Google Cloud não estava na mesma pasta raiz de onde o script Python estava sendo executado (ou estava com dupla extensão oculta pelo Windows). Movi o arquivo para a pasta correta ao lado do pipeline_bigquery.py, ajustei o nome e adicionei ao .gitignore para segurança.

Erro 2: Quebra de tipagem por causa de caracteres especiais

O que aconteceu: ValueError: could not convert string to float: 'Â42.96' na hora de converter o preço.

A Solução: O site usava Libras (£) e, durante o scraping, um problema de codificação puxou o caractere como Â£. Como eu só estava dando .replace("£", ""), o "Â" ficou para trás e quebrou o float. Resolvi deixando o código mais robusto, encadeando um .replace("Â", "") extra para limpar a string completamente antes da conversão.

Reflexões como Engenheira de Dados
Este projeto foi um divisor de águas. Foi o momento em que deixei de apenas rodar scripts locais que geram arquivos .csv e passei a integrar soluções reais de mercado operando na nuvem. Entender como a Google Cloud funciona, gerenciar permissões (IAM) e ver os dados fluindo do terminal do VS Code direto para um Dashboard executivo me deu muita confiança para atuar como Engenheira de Dados e Analista em projetos mais complexos.
