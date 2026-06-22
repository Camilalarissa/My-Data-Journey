# Bibliotecas e Frameworks

Este documento apresenta uma visão detalhada das principais bibliotecas e frameworks utilizados no desenvolvimento dos meus projetos de engenharia, análise de dados e Inteligência Artificial (como o **Smart Wardrobe Analytics**). 

A arquitetura das soluções foca na separação clara entre a camada de inteligência/processamento de dados (Backend e IA) e a camada de apresentação visual (Frontend).

---

##  Backend & Construção de APIs

###  1. FastAPI
O **FastAPI** é o componente central do ecossistema de backend. Trata-se de um framework web moderno, de alta performance, focado na construção de APIs RESTful robustas utilizando Python de forma assíncrona.
* **Aplicações Práticas:** Gerenciamento de regras de negócio, conexão com bases de dados e disponibilização de rotas (endpoints) seguras para consumo do frontend.
* **Diferenciais Técnicos:** Utiliza a biblioteca *Pydantic* para validação automática de tipos de dados em tempo de execução e gera automaticamente documentações interativas e auto-executáveis (Swagger UI), otimizando o tempo de teste e integração.

---

##  Interface Visual & Dashboards Interativos

###  2. Streamlit
O **Streamlit** é o framework utilizado para a prototipagem rápida e desenvolvimento da interface com o utilizador final.
* **Aplicações Práticas:** Transforma scripts Python estruturados em dashboards e aplicações web completas, interativas e visualmente profissionais, sem a necessidade de desenvolvimento nativo em HTML, CSS ou JavaScript.
* **Diferenciais Técnicos:** É a ferramenta padrão do mercado de dados para expor modelos de Inteligência Artificial, visualizações analíticas e filtros complexos utilizando apenas lógica de programação em Python.

---

##  Manipulação & Engenharia de Dados

###  3. Pandas
O **Pandas** é a ferramenta definitiva para análise, limpeza e manipulação de dados estruturados em Python.
* **Aplicações Práticas:** Atua na ingestão, tratamento de valores nulos, filtragem avançada, agrupamento estatístico e transformação de matrizes de dados antes de serem enviados para renderização gráfica ou armazenamento.
* **Diferenciais Técnicos:** Baseado na estrutura de *DataFrames* (tabelas bidimensionais otimizadas em memória), permitindo processar volumes massivos de dados com extrema velocidade e eficiência sintática.

---

##  Inteligência Artificial Generativa

###  4. google-genai
Esta é a biblioteca de SDK oficial da Google utilizada para conectar aplicações Python diretamente aos modelos fundacionais de última geração do ecossistema Gemini.
* **Aplicações Práticas:** Integração de recursos analíticos avançados, enriquecimento de dados de catálogo de forma preditiva, automação de categorizações e processamento de linguagem natural (NLP).
* **Diferenciais Técnicos:** Oferece acesso otimizado às ferramentas mais recentes da Google com baixa latência, suporte a chamadas assíncronas e estruturação segura de prompts diretamente no código.

---

##  Segurança & Gestão de Ambiente

### 5. python-dotenv
Uma biblioteca de utilidade essencial para conformidade com as melhores práticas de governança de dados e segurança de código.
* **Aplicações Práticas:** Carrega configurações e variáveis de ambiente a partir de um arquivo local isolado (o arquivo oculto `.env`), impedindo que credenciais sensíveis fiquem expostas no código fonte.
* **Diferenciais Técnicos:** Funciona em conjunto direto com o arquivo `.gitignore`. Chaves privadas, tokens de IA e strings de conexão a bancos de dados ou nuvens públicas ficam estritamente protegidos localmente, evitando vazamentos acidentais no GitHub (*Secret Scanning*).

---

##  Automação de Relatórios & Outputs

### 🖨️ 6. fpdf2 / WeasyPrint
Bibliotecas focadas na renderização, automação de layouts e exportação de documentos estruturados.
* **Aplicações Práticas:** Permitem que o utilizador final faça a exportação de relatórios analíticos, tabelas consolidadas e métricas da aplicação diretamente para o formato físico PDF com apenas um clique.
* **Diferenciais Técnicos:** O *FPDF2* permite uma construção programática precisa de vetores e textos, enquanto o *WeasyPrint* converte estruturas complexas de HTML/CSS diretamente em páginas PDF prontas para impressão, mantendo a consistência do design.
