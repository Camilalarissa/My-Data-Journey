# 📓 Notebooks & Colaboração — Databricks Fundamentals

> **Exame:** Databricks Fundamentals
> **Tópico:** Módulo 2 — Linguagens e comandos mágicos, colaboração em tempo real, controle de versão e visualizações nativas.

---

## 📑 Índice
1. [O que é um notebook no Databricks](#1-o-que-é-um-notebook-no-databricks)
2. [Linguagens e comandos mágicos](#2-linguagens-e-comandos-mágicos)
3. [Colaboração](#3-colaboração)
4. [Visualizações nativas](#4-visualizações-nativas)
5. [Resumo mental para a prova](#5-resumo-mental-para-a-prova)
6. [Possíveis pegadinhas da prova](#6-possíveis-pegadinhas-da-prova)
7. [Questões no estilo da prova](#7-questões-no-estilo-da-prova)

---

## 1. O que é um notebook no Databricks

Um notebook é um documento interativo formado por **células**. Cada célula pode ter código, texto ou um comando especial. Para rodar, o notebook precisa estar **anexado (attached) a um compute** (veja *Módulo 1: Gestão de Clusters*).

Todo notebook tem uma **linguagem padrão**, escolhida na criação: Python, SQL, Scala ou R. As células sem comando mágico usam essa linguagem.

---

## 2. Linguagens e comandos mágicos

Para usar outra linguagem numa célula específica, coloque o comando mágico **na primeira linha da célula**. Ele muda a linguagem **só daquela célula**.

| Comando | O que faz |
| :--- | :--- |
| `%python` | Executa a célula em Python |
| `%sql` | Executa a célula em SQL |
| `%scala` | Executa a célula em Scala |
| `%r` | Executa a célula em R |
| `%md` | Célula de texto em Markdown (títulos, listas, documentação) |
| `%sh` | Executa comandos de shell **no driver** |
| `%fs` | Atalho para comandos de arquivos (`dbutils.fs`) |
| `%run` | Executa outro notebook **dentro** do atual |
| `%pip` | Instala bibliotecas Python para o notebook |

### Exemplo: misturando Python e SQL

```python
# Célula 1 (Python, a linguagem padrão do notebook)
df = spark.read.table("bronze.vendas.pedidos")
df.createOrReplaceTempView("pedidos")
```

```sql
%sql
-- Célula 2 (SQL)
SELECT status, COUNT(*) AS total
FROM pedidos
GROUP BY status
```

### Variáveis não cruzam linguagens

Cada linguagem roda num ambiente próprio. Uma variável `df` criada em Python **não existe** numa célula `%scala` ou `%sql`. Para compartilhar dados entre linguagens, use algo que todas enxergam:

- uma **temp view** (`df.createOrReplaceTempView("nome")`), como no exemplo acima;
- uma **tabela**.

> 💡 **Bônus:** quando você roda `%sql` num notebook Python, o resultado fica disponível no Python como o DataFrame `_sqldf`.

### Dois comandos que caem bastante

- **`%run ./utils`:** executa o outro notebook **no mesmo contexto**. As funções e variáveis dele passam a existir no notebook atual. É a forma clássica de reaproveitar código.
- **`%sh`:** executa o comando **só no driver**. Os workers não executam esse comando.

---

## 3. Colaboração

### Compartilhamento e permissões

Você compartilha pelo botão **Share**, escolhendo usuários ou grupos e um nível de permissão. Cada nível inclui os anteriores:

| Permissão | O que permite |
| :--- | :--- |
| **CAN READ** | Ver o notebook e os resultados |
| **CAN RUN** | Ver e executar células |
| **CAN EDIT** | Ver, executar e editar o código |
| **CAN MANAGE** | Tudo isso e também alterar as permissões dos outros |

Também dá para dar permissão a uma **pasta**, e os notebooks dentro dela herdam essa permissão.

### Edição em tempo real

Várias pessoas podem editar o mesmo notebook **ao mesmo tempo**, como no Google Docs. Você vê o cursor dos outros e as mudanças aparecem na hora.

### Comentários

Você seleciona um trecho de código e adiciona um comentário. É útil para revisão de código e dúvidas, sem alterar o código em si.

### Controle de versão: dois níveis

| Recurso | Como funciona | Quando usar |
| :--- | :--- | :--- |
| **Histórico de versões** (*version history*) | O Databricks salva revisões **automaticamente**; você pode ver versões antigas e **restaurar** uma delas | Recuperar algo no dia a dia; não substitui o Git |
| **Git folders** (antigo *Repos*) | Conecta o notebook a um repositório (GitHub, GitLab, Azure Repos, Bitbucket), com branches, commits, pull e push | Trabalho em equipe e **CI/CD** (o tema volta no Módulo 5) |

---

## 4. Visualizações nativas

### `display()` é a porta de entrada

Em vez de `df.show()`, que imprime texto simples, use `display(df)`. O resultado vira uma **tabela interativa**, onde você pode ordenar, filtrar e copiar dados. Resultados de células `%sql` já aparecem assim automaticamente.

```python
display(df)   # tabela interativa
df.show()     # texto simples, sem gráficos
```

A partir dessa tabela, você clica em **"+"** e escolhe entre duas opções:

- **Visualization:** cria um gráfico (barras, linhas, pizza, mapa, dispersão, tabela dinâmica/pivot e outros) sem escrever código.
- **Data Profile:** gera um resumo estatístico de cada coluna (contagem, nulos, média, mínimo, máximo e distribuição). É ótimo para conhecer um dataset novo.

### Dashboards

Os gráficos podem ser reunidos em painéis:

- **Dashboards de notebook** (legado): agrupam saídas de células do próprio notebook.
- **AI/BI Dashboards** (o recomendado hoje): painéis feitos para compartilhar com a área de negócio, rodando sobre **SQL Warehouses**.

### Widgets (parâmetros)

Com `dbutils.widgets` você cria caixas de texto ou listas suspensas no topo do notebook. Assim o mesmo notebook roda com valores diferentes, como uma data ou um ambiente. Eles são muito usados quando o notebook vira tarefa de um **Job**.

```python
dbutils.widgets.text("data_ref", "2026-11-01")
data_ref = dbutils.widgets.get("data_ref")
```

---

## 5. Resumo mental para a prova

- O comando mágico vai na **primeira linha** da célula e muda a linguagem **só daquela célula**.
- **Variáveis não cruzam linguagens.** Para compartilhar, use temp view ou tabela.
- `%run` executa outro notebook no mesmo contexto; `%md` é documentação; `%sh` roda no driver.
- Permissões: **READ → RUN → EDIT → MANAGE**.
- Histórico de versões é **automático** e simples; Git folders são o versionamento **de verdade**, com branches e CI/CD.
- `display()` gera tabela interativa, gráficos e Data Profile sem código.

---

## 6. Possíveis pegadinhas da prova

| ❌ Afirmação falsa (armadilha) | ✅ O correto |
| :--- | :--- |
| "Uma variável criada em Python pode ser usada numa célula `%scala`." | Cada linguagem tem seu contexto. Compartilhe via temp view ou tabela. |
| "`%sql` muda a linguagem padrão do notebook inteiro." | Muda só aquela célula. A linguagem padrão se altera nas configurações do notebook. |
| "O histórico de versões substitui o Git." | O histórico é automático e simples. Branches, pull requests e CI/CD exigem Git folders. |
| "Quem tem CAN RUN pode editar o código." | CAN RUN só executa. Editar exige CAN EDIT. |
| "Só uma pessoa pode editar o notebook por vez." | A edição é simultânea, em tempo real. |
| "`%sh` executa o comando em todos os nós do cluster." | Executa só no driver. |
| "Para criar gráfico é preciso usar matplotlib." | `display()` já oferece gráficos nativos sem código. |
| "`df.show()` e `display(df)` são a mesma coisa." | `show()` imprime texto; `display()` gera tabela interativa com gráficos e Data Profile. |

> 💡 **Dica de leitura:** quando a pergunta falar em "compartilhar dados entre linguagens", pense em **temp view**. Quando falar em "branches" ou "CI/CD", pense em **Git folders**.

---

## 7. Questões no estilo da prova

**1. Em um notebook cuja linguagem padrão é Python, como executar uma única célula em SQL?**
- a) Mudar a linguagem padrão do notebook para SQL
- b) Escrever `%sql` na primeira linha da célula
- c) Criar um novo notebook em SQL e usar `%run`
- d) Não é possível misturar linguagens no mesmo notebook

**2. Um DataFrame `df` foi criado em Python. Qual é a forma correta de consultá-lo numa célula `%sql` do mesmo notebook?**
- a) Usar `SELECT * FROM df` diretamente, pois as variáveis são compartilhadas
- b) Registrar o DataFrame como temp view e consultar a view
- c) Exportar o notebook para `.ipynb`
- d) Usar `%sh` para copiar a variável

**3. Qual comando executa outro notebook dentro do atual, tornando suas funções e variáveis disponíveis?**
- a) `%md`
- b) `%fs`
- c) `%run`
- d) `%pip`

**4. Uma analista deve poder executar as células de um notebook, mas não alterar o código. Qual permissão mínima ela precisa?**
- a) CAN READ
- b) CAN RUN
- c) CAN EDIT
- d) CAN MANAGE

**5. Uma equipe quer trabalhar com branches, pull requests e CI/CD nos notebooks. Qual recurso usar?**
- a) Histórico de versões do notebook
- b) Comentários nas células
- c) Git folders conectados a um provedor Git
- d) Exportar o notebook em HTML

**6. Qual a forma mais rápida de criar um gráfico e ver estatísticas das colunas de um DataFrame, sem escrever código de visualização?**
- a) Usar `df.show()` e copiar os dados para uma planilha
- b) Usar `display(df)` e adicionar uma Visualization ou um Data Profile
- c) Instalar matplotlib com `%pip`
- d) Rodar `%sh` para gerar um gráfico

<details>
<summary><b>📝 Gabarito (clique para abrir)</b></summary>

| Questão | Resposta | Por quê |
| :--- | :--- | :--- |
| 1 | b | O comando mágico muda a linguagem só daquela célula. |
| 2 | b | Variáveis não cruzam linguagens; a temp view é enxergada por todas. |
| 3 | c | `%run` executa o outro notebook no mesmo contexto. |
| 4 | b | CAN RUN permite ver e executar, sem editar. |
| 5 | c | Branches, PRs e CI/CD exigem Git folders; o histórico de versões é simples. |
| 6 | b | `display()` oferece gráficos nativos e Data Profile sem código. |

</details>
