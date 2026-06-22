# Análise de Sentimentos em E-commerce (Sentiment Analysis Project)

##  Objetivo do Projeto
O objetivo principal deste projeto é desenvolver um pipeline automatizado de processamento de dados capaz de coletar, tratar e classificar o sentimento (positivo, negativo ou neutro) de avaliações de clientes (*reviews*) em plataformas de e-commerce. A meta final é transformar dados textuais brutos e não estruturados em insights de negócio acionáveis, mapeando as principais dores dos clientes e identificando pontos fortes de produtos automaticamente.

---

##  Problema de Negócio & Metas
*   **O Problema:** Feedbacks e avaliações de clientes chegam em grande volume a todo momento. Analisar cada texto manualmente é inviável, lento e propenso a falhas humanas de interpretação.
*   **A Meta:** 
    *   Construir uma ferramenta escalável para categorizar o tom dos comentários com alta acurácia.
    *   Exibir os resultados em um painel interativo (dashboard) focado em tomada de decisão (mudar embalagens, alterar rotas de entrega, reavaliar qualidade de fornecedores).

---

##  Stack Tecnológica & Por Que Destas Escolhas

A arquitetura do projeto foi desenhada utilizando tecnologias modernas do ecossistema de Dados e Inteligência Artificial em Python:

### 1. Python & Pandas (Engenharia e Tratamento de Dados)
*   **Porquê:** O **Pandas** é a ferramenta padrão para o tratamento de dados estruturados. Foi escolhido pela eficiência na limpeza do texto bruto (remoção de caracteres especiais, tratamento de linhas nulas e padronização textual) através de *DataFrames*, preparando a base para a modelagem.

### 2. Google GenAI (Gemini SDK)
*   **Porquê:** Em vez de depender apenas de dicionários estáticos de palavras, a biblioteca **google-genai** foi escolhida para integrar modelos de Large Language Models (LLMs). Isso permite que o sistema compreenda ironias, nuances de linguagem, gírias locais e contextos complexos que abordagens tradicionais de Machine Learning (como TF-IDF simples) não pegariam.

### 3. Streamlit (Apresentação e Análise Visual)
*   **Porquê:** O **Streamlit** permite converter a lógica de análise de sentimentos em uma aplicação web interativa em tempo recorde. Foi escolhido para que gestores de e-commerce possam filtrar as avaliações por data, nota, produto ou categoria sem precisar interagir com linhas de código.

### 4. python-dotenv (Segurança e Governança)
*   **Porquê:** Segurança em primeiro lugar. A biblioteca foi implementada para isolar a chave de API (API Key) do ecossistema Google dentro de um ambiente seguro local (`.env`). Isso garante conformidade com as regras de *Secret Scanning* do GitHub, eliminando riscos de vazamento de credenciais na nuvem pública.

---

##  Como Executar o Projeto Localmente

1. **Clonar o repositório:**
```bash
   git clone [https://github.com/seu-usuario/sentiment-analysis-ecommerce.git](https://github.com/seu-usuario/sentiment-analysis-ecommerce.git)
   cd sentiment-analysis-ecommerce
