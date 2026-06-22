# Checklist Streamlit

<div align="center">
  <img src="https://img.shields.io/badge/Python-Data_Engineering-%23704214?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Streamlit-Framework-%23FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white" alt="Streamlit">
</div>

---

Este guia documenta os conceitos essenciais do **Streamlit**, uma biblioteca de Python que permite construir aplicações web interativas e painéis de dados profissionais sem a necessidade de escrever HTML, CSS ou JavaScript. 

Como **Engenheira de Dados**, esta é a ferramenta ponte para transformar scripts analíticos de *back-end* em produtos de dados interativos para o cliente final.

## 1. ⚙️ Como o Streamlit Funciona (O Motor)
- [ ] **Execução Top-Down:** Sempre que o utilizador interage com um botão ou campo de texto, o Streamlit executa o script inteiro de cima a baixo. É fundamental entender este fluxo para gerir a performance.
- [ ] **Comando de Execução:** O script nunca deve ser executado com `python ficheiro.py`. Levanta-se o servidor web local utilizando o terminal:
  ```bash
  streamlit run ficheiro.py

 ## 2. 📊 Exibição de Dados (O Core da Engenheira)
[ ] st.dataframe(df): A forma ideal de apresentar dados. Permite que o utilizador navegue na tabela, ordene colunas e descarregue os dados em formato CSV nativamente.

[ ] st.table(df): Gera uma tabela estática (sem interatividade). Excelente para pequenos resumos, mas não recomendada para grandes bases de dados.

[ ] st.metric("Rótulo", "Valor", "Variação"): Cria scorecards ou KPIs clássicos para painéis executivos (ex: Lucro Total com uma seta de crescimento).

[ ] st.plotly_chart(fig): Função nativa para injetar gráficos interativos desenhados com a biblioteca Plotly.

##  3. 🖱️ Interatividade (Inputs do Utilizador)
[ ] st.button("Clique Aqui"): Executa uma ação quando acionado. Normalmente associado a uma estrutura condicional:

if st.button("Analisar Dados"):
    processar_pipeline()

## 4. 🎨 Layout e Estrutura (Design da Interface)
[ ] st.set_page_config(layout="wide"): Deve ser rigorosamente a primeira linha de código do script. O parâmetro wide expande o painel para ocupar todo o ecrã do monitor, garantindo um aspeto executivo.

[ ] col1, col2 = st.columns(2): Divide o ecrã verticalmente. Fundamental para posicionar gráficos lado a lado.

[ ] aba1, aba2 = st.tabs(["Aba 1", "Aba 2"]): Cria um sistema de navegação por abas, evitando a sobrecarga de informação visual num único ecrã.

[ ] with st.sidebar:: Direciona elementos (filtros, textos, imagens) para uma barra lateral à esquerda, libertando o corpo principal do ecrã para os gráficos mais importantes.

## 5. 🚀 Performance (Práticas de Nível Sénior)
Como o Streamlit recarrega o código a cada clique, operações pesadas (como queries SQL ou carregamento de Inteligência Artificial) devem ser protegidas para não congelarem a aplicação.

[ ] @st.cache_data: Decorador utilizado antes de uma função de extração de dados. Guarda o resultado em memória; se o utilizador clicar novamente num filtro, o Streamlit não repete o processamento da base de dados.

[ ] @st.cache_resource: Decorador focado em armazenar ligações abertas a bases de dados ou modelos de Machine Learning pesados, carregando-os uma única vez para a memória global.

[ ] st.session_state: Um dicionário especial embutido que permite guardar variáveis e estados que não devem ser apagados durante o recarregamento contínuo da página.

## 6. ☁️ Deploy (Colocar na Nuvem)
[ ] Streamlit Community Cloud: A infraestrutura oficial para colocar projetos de portefólio no ar rapidamente, ligando diretamente ao repositório do GitHub.

[ ] Docker + Cloud Run (GCP) / AWS: Para implementações corporativas. A aplicação Streamlit é encapsulada num contentor Docker e alojada num ambiente cloud escalável e seguro.
