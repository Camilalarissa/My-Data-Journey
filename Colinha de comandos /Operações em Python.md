# 🐍 Cheat Sheet: Comandos Python mais usados em Dados

Guia de referência rápida com os comandos Python que mais aparecem no dia a dia de **engenharia e análise de dados**. Cada seção explica **para que serve** e traz exemplos prontos para copiar.

> 🧭 **Convenções deste guia**
> - Os exemplos usam um DataFrame de vendas como base: `df` com as colunas `id_venda`, `data_venda`, `cliente`, `uf`, `categoria`, `valor` e `status`.
> - Compatível com **pandas 2.x e 3.x**. Evite `inplace=True`: no pandas 3.0 (Copy-on-Write) ele pode não alterar o DataFrame original. Prefira sempre `df = df.metodo(...)`.
> - Para PySpark e comandos do Databricks, veja [`Comandos no Databricks.md`](Comandos%20no%20Databricks.md). No fim deste guia há uma tabela de equivalência **pandas ↔ PySpark**.

---

## 📑 Índice
1. [Python essencial para dados](#1--python-essencial-para-dados)
2. [Arquivos, JSON e caminhos](#2--arquivos-json-e-caminhos)
3. [Datas e horas](#3--datas-e-horas)
4. [Pandas: leitura e escrita](#4--pandas-leitura-e-escrita)
5. [Pandas: inspeção](#5--pandas-inspeção)
6. [Pandas: seleção e filtros](#6--pandas-seleção-e-filtros)
7. [Pandas: limpeza](#7--pandas-limpeza)
8. [Pandas: transformação de colunas](#8--pandas-transformação-de-colunas)
9. [Pandas: agregações e tabelas dinâmicas](#9--pandas-agregações-e-tabelas-dinâmicas)
10. [Pandas: juntar DataFrames](#10--pandas-juntar-dataframes)
11. [Pandas: séries temporais](#11--pandas-séries-temporais)
12. [NumPy essencial](#12--numpy-essencial)
13. [APIs e bancos de dados](#13--apis-e-bancos-de-dados)
14. [Boas práticas: erros, logs e ambiente](#14--boas-práticas-erros-logs-e-ambiente)
15. [Tabela-resumo: "Quero fazer X, uso Y"](#15--tabela-resumo-quero-fazer-x-uso-y)
16. [Equivalência pandas ↔ PySpark](#16--equivalência-pandas--pyspark)

---

## 1. 🧱 Python essencial para dados

### Listas, dicionários e conjuntos

```python
valores = [120.5, 89.9, 230.0, 89.9]
cadastro = {"id": 1, "nome": "Ana", "uf": "SP"}
ufs = {"SP", "RJ", "SP", "MG"}           # set: remove duplicatas → {'SP', 'RJ', 'MG'}

len(valores)                              # tamanho
sum(valores), max(valores), min(valores)  # agregações rápidas
sorted(valores, reverse=True)             # ordenar (nova lista)
cadastro.get("email", "sem email")        # ler chave com valor padrão (não dá erro)
cadastro.keys(), cadastro.values(), cadastro.items()
```

### List comprehension e dict comprehension

Forma curta de criar listas e dicionários a partir de outros. Muito usada para limpar dados.

```python
# Filtrar e transformar em uma linha
acima_de_100 = [v for v in valores if v > 100]
com_desconto = [round(v * 0.9, 2) for v in valores]

# Dicionário a partir de duas listas
colunas = ["id", "nome", "uf"]
linha = [1, "Ana", "SP"]
registro = dict(zip(colunas, linha))     # {'id': 1, 'nome': 'Ana', 'uf': 'SP'}

# Dict comprehension: padronizar nomes de colunas
renomear = {c: c.strip().lower().replace(" ", "_") for c in ["Nome Cliente", " UF "]}
```

### Funções

```python
def calcular_ticket_medio(faturamento: float, qtd_vendas: int) -> float:
    """Retorna o ticket médio; evita divisão por zero."""
    if qtd_vendas == 0:
        return 0.0
    return round(faturamento / qtd_vendas, 2)

calcular_ticket_medio(1500.0, 12)

# Função anônima (lambda): útil em ordenações e no pandas
pedidos = [{"id": 1, "valor": 50}, {"id": 2, "valor": 300}]
mais_caros = sorted(pedidos, key=lambda p: p["valor"], reverse=True)
```

### Strings

```python
texto = "  Camila Larissa  "
texto.strip()                     # remove espaços nas pontas
texto.lower(), texto.upper(), texto.title()
"59,90".replace(",", ".")         # vírgula decimal → ponto
"SP-01-2026".split("-")           # ['SP', '01', '2026']
"_".join(["vendas", "2026"])      # 'vendas_2026'
"arquivo.csv".endswith(".csv")    # True

total = 1234.5
f"Faturamento: R$ {total:,.2f}"   # 'Faturamento: R$ 1,234.50'
f"{0.1834:.1%}"                   # '18.3%'
```

### Laços úteis

```python
for i, valor in enumerate(valores, start=1):    # índice + valor
    print(i, valor)

for nome, uf in zip(["Ana", "Bia"], ["SP", "RJ"]):  # percorrer duas listas juntas
    print(nome, uf)
```

---

## 2. 📁 Arquivos, JSON e caminhos

### Caminhos com `pathlib`

Mais seguro que montar caminhos com texto, e funciona no Windows, Linux e Mac.

```python
from pathlib import Path

pasta = Path("dados")
pasta.mkdir(parents=True, exist_ok=True)       # cria a pasta (sem erro se já existir)
arquivo = pasta / "vendas.csv"                 # junta caminhos com /
arquivo.exists(), arquivo.suffix, arquivo.stem # True/False, '.csv', 'vendas'

for csv_encontrado in pasta.glob("*.csv"):     # listar arquivos
    print(csv_encontrado.name)
```

### Ler e escrever texto e JSON

```python
import json

# Escrever e ler JSON
dados = {"pedido": 1, "itens": [{"sku": "A1", "qtd": 2}]}
with open(pasta / "pedido.json", "w", encoding="utf-8") as f:
    json.dump(dados, f, ensure_ascii=False, indent=2)

with open(pasta / "pedido.json", encoding="utf-8") as f:
    pedido = json.load(f)

# Texto JSON <-> dicionário
texto_json = json.dumps(dados, ensure_ascii=False)
de_volta = json.loads(texto_json)
```

> 💡 Sempre informe `encoding="utf-8"` ao abrir arquivos. É o que evita o famoso `Â£` e os acentos quebrados.

### CSV sem pandas (arquivos muito simples)

```python
import csv

with open(pasta / "clientes.csv", "w", newline="", encoding="utf-8") as f:
    escritor = csv.writer(f, delimiter=";")
    escritor.writerow(["id", "nome"])
    escritor.writerow([1, "Ana"])

with open(pasta / "clientes.csv", encoding="utf-8") as f:
    for linha in csv.DictReader(f, delimiter=";"):
        print(linha["id"], linha["nome"])
```

---

## 3. 📅 Datas e horas

```python
from datetime import date, datetime, timedelta
from zoneinfo import ZoneInfo

hoje = date.today()
agora_sp = datetime.now(ZoneInfo("America/Sao_Paulo"))   # com fuso horário

ontem = hoje - timedelta(days=1)
daqui_30 = hoje + timedelta(days=30)

# Texto → data
data = datetime.strptime("22/10/2026", "%d/%m/%Y").date()
iso = date.fromisoformat("2026-10-22")

# Data → texto
hoje.strftime("%d/%m/%Y")      # '09/11/2026'
hoje.isoformat()               # '2026-11-09'

# Diferença entre datas
(date(2026, 12, 31) - hoje).days
```

| Código | Significado | Exemplo |
| :--- | :--- | :--- |
| `%Y` / `%y` | Ano com 4 / 2 dígitos | 2026 / 26 |
| `%m` | Mês | 11 |
| `%d` | Dia | 09 |
| `%H:%M:%S` | Hora, minuto, segundo | 14:30:00 |

---

## 4. 📥 Pandas: leitura e escrita

```python
import pandas as pd
```

### Leitura

```python
# CSV brasileiro: separador ; e vírgula decimal
df = pd.read_csv(
    "dados/vendas.csv",
    sep=";",
    decimal=",",
    encoding="utf-8",
    dtype={"id_venda": "string"},     # forçar o tipo de colunas-chave
    parse_dates=["data_venda"],       # já converte para data
)

df_excel = pd.read_excel("dados/metas.xlsx", sheet_name="2026")  # requer openpyxl
df_parquet = pd.read_parquet("dados/vendas.parquet")             # requer pyarrow
df_json = pd.read_json("dados/eventos.json", lines=True)         # JSON Lines (um por linha)

# JSON aninhado → tabela plana
registros = [{"id": 1, "cliente": {"nome": "Ana", "uf": "SP"}}]
plano = pd.json_normalize(registros)    # colunas: id, cliente.nome, cliente.uf
```

### Escrita

```python
df.to_csv("dados/saida.csv", sep=";", decimal=",", index=False, encoding="utf-8")
df.to_parquet("dados/saida.parquet", index=False)   # formato recomendado para dados
df.to_excel("dados/saida.xlsx", index=False)
```

> 💡 Use `index=False` quase sempre: sem ele, o pandas grava o índice como uma coluna extra.

### Criar um DataFrame do zero

```python
df = pd.DataFrame({
    "id_venda": ["V1", "V2", "V3", "V4", "V4"],
    "data_venda": pd.to_datetime(["2026-10-01", "2026-10-01", "2026-10-02", "2026-10-03", "2026-10-03"]),
    "cliente": [" ana silva", "BRUNO LIMA", None, "Carla Dias", "Carla Dias"],
    "uf": ["SP", "rj", "SP", "MG", "MG"],
    "categoria": ["Blusas", "Calças", "Blusas", "Tênis", "Tênis"],
    "valor": [120.5, 89.9, None, 230.0, 230.0],
    "status": ["pago", "pago", "cancelado", "pago", "pago"],
})
```

---

## 5. 🔎 Pandas: inspeção

O primeiro passo com qualquer dado novo: **entender o que chegou** antes de transformar.

```python
df.head(10)                  # primeiras linhas (df.tail() para as últimas)
df.sample(5, random_state=1) # amostra aleatória
df.shape                     # (linhas, colunas)
df.columns                   # nomes das colunas
df.dtypes                    # tipo de cada coluna
df.info()                    # tipos + valores não nulos + memória
df.describe()                # estatísticas das colunas numéricas
df.describe(include="all")   # inclui colunas de texto

df.isna().sum()                         # nulos por coluna
df["uf"].value_counts()                 # frequência de cada valor
df["uf"].value_counts(normalize=True)   # em proporção
df["categoria"].nunique()               # quantidade de valores distintos
df["categoria"].unique()                # os valores distintos
df.duplicated().sum()                   # linhas totalmente duplicadas
```

---

## 6. 🎯 Pandas: seleção e filtros

```python
df["valor"]                     # uma coluna (Series)
df[["cliente", "valor"]]        # várias colunas (DataFrame)

# loc: por RÓTULO (nome da coluna / condição)
df.loc[df["valor"] > 100, ["cliente", "valor"]]

# iloc: por POSIÇÃO
df.iloc[0]          # primeira linha
df.iloc[:3, :2]     # 3 primeiras linhas, 2 primeiras colunas

# Filtros com várias condições: use & (e), | (ou), ~ (não) e parênteses
pagos_sp = df[(df["status"] == "pago") & (df["uf"] == "SP")]
fora_sp = df[~(df["uf"] == "SP")]
sul_sudeste = df[df["uf"].isin(["SP", "RJ", "MG", "PR"])]
faixa = df[df["valor"].between(50, 150)]
com_ana = df[df["cliente"].str.contains("ana", case=False, na=False)]

# query: filtro em texto (bom para leitura)
df.query("status == 'pago' and valor > 100")

# Ordenar e pegar os maiores
df.sort_values(["data_venda", "valor"], ascending=[True, False])
df.nlargest(3, "valor")
```

> ⚠️ Em filtros, use `&` e `|`, nunca `and` e `or`, e coloque **cada condição entre parênteses**.

---

## 7. 🧹 Pandas: limpeza

```python
# Nulos
df = df.dropna(subset=["id_venda"])                             # remove linhas sem a chave
df = df.fillna({"valor": 0, "cliente": "Não identificado"})     # preenche por coluna

# Duplicatas
df = df.drop_duplicates()                                       # linhas idênticas
df = df.drop_duplicates(subset=["id_venda"], keep="last")       # pela chave, mantém a última

# Padronizar texto
df["cliente"] = df["cliente"].str.strip().str.title()
df["uf"] = df["uf"].str.upper()

# Converter tipos
df["valor"] = pd.to_numeric(df["valor"], errors="coerce")       # texto inválido vira NaN
df["data_venda"] = pd.to_datetime(df["data_venda"], errors="coerce")
df["id_venda"] = df["id_venda"].astype("string")

# Número em formato brasileiro guardado como texto: "1.234,56"
serie_br = pd.Series(["1.234,56", "59,90"])
numeros = pd.to_numeric(serie_br.str.replace(".", "", regex=False).str.replace(",", ".", regex=False))

# Renomear e remover colunas
df = df.rename(columns={"valor": "valor_venda"})
df.columns = [c.strip().lower().replace(" ", "_") for c in df.columns]   # padronizar todas
df = df.drop(columns=["status"])
```

---

## 8. 🔄 Pandas: transformação de colunas

```python
import numpy as np

df = df.rename(columns={"valor_venda": "valor"})   # (voltando ao nome original)

# Criar colunas a partir de outras
df["valor_com_frete"] = df["valor"] + 15
df = df.assign(
    valor_desconto=lambda d: d["valor"] * 0.9,
    ano_mes=lambda d: d["data_venda"].dt.strftime("%Y-%m"),
)

# Condicional simples: np.where(condição, se verdadeiro, se falso)
df["tipo"] = np.where(df["valor"] >= 100, "alto", "baixo")

# Várias condições: np.select
condicoes = [df["valor"] >= 200, df["valor"] >= 100]
df["faixa"] = np.select(condicoes, ["premium", "médio"], default="básico")

# Faixas numéricas: pd.cut
df["faixa_valor"] = pd.cut(df["valor"], bins=[0, 100, 200, float("inf")], labels=["até 100", "100-200", "200+"])

# Mapear valores com um dicionário
regiao = {"SP": "Sudeste", "RJ": "Sudeste", "MG": "Sudeste"}
df["regiao"] = df["uf"].map(regiao)

# Função linha a linha (use só quando não houver operação vetorizada: é lento)
df["descricao"] = df.apply(lambda linha: f"{linha['categoria']} - {linha['uf']}", axis=1)

# Texto: dividir uma coluna em várias
df_split = pd.DataFrame({"codigo": ["SP-001", "RJ-002"]})
df_split[["uf", "loja"]] = df_split["codigo"].str.split("-", expand=True)

# Lista em uma célula → uma linha por item
pedidos = pd.DataFrame({"id": [1, 2], "itens": [["A", "B"], ["C"]]})
pedidos.explode("itens")
```

> 💡 Prefira operações **vetorizadas** (`+`, `np.where`, `.str`, `.dt`) a `apply` e laços `for`: podem ser dezenas de vezes mais rápidas.

---

## 9. 📊 Pandas: agregações e tabelas dinâmicas

```python
# Agregação simples
df["valor"].sum(), df["valor"].mean(), df["valor"].median()

# groupby com agregações nomeadas (forma recomendada)
resumo = (
    df.groupby(["uf", "categoria"], as_index=False)
    .agg(
        qtd_vendas=("id_venda", "nunique"),
        faturamento=("valor", "sum"),
        ticket_medio=("valor", "mean"),
    )
    .sort_values("faturamento", ascending=False)
)

# Participação de cada grupo no total
resumo["participacao"] = resumo["faturamento"] / resumo["faturamento"].sum()

# transform: devolve o resultado do grupo em cada linha (mesmo tamanho do df)
df["total_uf"] = df.groupby("uf")["valor"].transform("sum")

# Tabela dinâmica (linhas × colunas)
pivo = df.pivot_table(index="uf", columns="categoria", values="valor", aggfunc="sum", fill_value=0)

# O caminho inverso: colunas → linhas (formato longo)
longo = pivo.reset_index().melt(id_vars="uf", var_name="categoria", value_name="faturamento")

# Contagem cruzada
pd.crosstab(df["uf"], df["categoria"])
```

---

## 10. 🔗 Pandas: juntar DataFrames

```python
clientes = pd.DataFrame({"cliente": ["Ana Silva", "Bruno Lima"], "segmento": ["Ouro", "Prata"]})

# merge = JOIN do SQL
completo = df.merge(clientes, on="cliente", how="left")         # inner, left, right, outer

# Chaves com nomes diferentes
lojas = pd.DataFrame({"sigla": ["SP", "MG"], "gerente": ["Rita", "Paulo"]})
com_gerente = df.merge(lojas, left_on="uf", right_on="sigla", how="left")

# Conferir o resultado do join (ótimo para achar registros sem correspondência)
diagnostico = df.merge(clientes, on="cliente", how="left", indicator=True)
diagnostico["_merge"].value_counts()      # both / left_only / right_only

# concat = UNION ALL do SQL (empilhar DataFrames com as mesmas colunas)
jan = df.head(2)
fev = df.tail(2)
ano = pd.concat([jan, fev], ignore_index=True)
```

> ⚠️ Depois de um `merge`, confira se o número de linhas mudou: chaves duplicadas do lado direito **multiplicam** linhas. Use `validate="many_to_one"` para o pandas avisar.

```python
df.merge(clientes, on="cliente", how="left", validate="many_to_one")
```

---

## 11. ⏱️ Pandas: séries temporais

```python
serie = (
    df.groupby("data_venda", as_index=False)["valor"].sum()
    .set_index("data_venda")
    .sort_index()
)

# Componentes da data com .dt
df["ano"] = df["data_venda"].dt.year
df["mes"] = df["data_venda"].dt.month
df["dia_semana"] = df["data_venda"].dt.day_name()
df["semana"] = df["data_venda"].dt.isocalendar().week

# Reagrupar por período (D = dia, W = semana, MS = início do mês)
semanal = serie.resample("W").sum()

# Comparações no tempo
serie["valor_dia_anterior"] = serie["valor"].shift(1)
serie["variacao_pct"] = serie["valor"].pct_change()
serie["media_movel_7d"] = serie["valor"].rolling(window=7, min_periods=1).mean()
serie["acumulado"] = serie["valor"].cumsum()
```

---

## 12. 🔢 NumPy essencial

```python
import numpy as np

a = np.array([10, 20, 30, 40])
a * 2                         # operação em todos os elementos
a.mean(), a.std(), a.sum()
np.percentile(a, 90)          # percentil 90
np.round(a / 3, 2)
np.where(a > 25, "alto", "baixo")
np.nan                        # valor ausente
np.isnan(np.array([1.0, np.nan]))
```

---

## 13. 🌐 APIs e bancos de dados

### Consumir uma API REST com `requests`

```python
import requests

resposta = requests.get(
    "https://api.exemplo.com/v1/pedidos",
    params={"data": "2026-11-09", "pagina": 1},
    headers={"Authorization": "Bearer <token>"},   # leia o token de variável de ambiente
    timeout=30,                                     # sempre defina um timeout
)
resposta.raise_for_status()      # gera erro se o status for 4xx ou 5xx
dados = resposta.json()
df_api = pd.json_normalize(dados["resultados"])
```

### Paginação

```python
todos = []
pagina = 1
while True:
    r = requests.get("https://api.exemplo.com/v1/pedidos", params={"pagina": pagina}, timeout=30)
    r.raise_for_status()
    lote = r.json()["resultados"]
    if not lote:
        break
    todos.extend(lote)
    pagina += 1
```

### Banco de dados com SQLAlchemy

```python
from sqlalchemy import create_engine

engine = create_engine("postgresql+psycopg2://usuario:senha@host:5432/banco")  # use variáveis de ambiente

df_banco = pd.read_sql("SELECT * FROM vendas WHERE data_venda >= '2026-10-01'", engine)
df.to_sql("vendas_tratadas", engine, if_exists="append", index=False)
```

---

## 14. 🛡️ Boas práticas: erros, logs e ambiente

### Tratamento de erros

```python
try:
    df_metas = pd.read_csv("dados/metas.csv", sep=";")
except FileNotFoundError:
    print("Arquivo de metas não encontrado; seguindo sem metas.")
    df_metas = pd.DataFrame()
```

### Logs em vez de `print`

```python
import logging

logging.basicConfig(level=logging.INFO, format="%(asctime)s %(levelname)s %(message)s")
log = logging.getLogger("pipeline_vendas")

log.info("Linhas lidas: %s", len(df))
log.warning("Vendas sem cliente: %s", df["cliente"].isna().sum())
```

### Segredos fora do código

```python
import os

token = os.getenv("API_TOKEN")    # nunca escreva senhas e tokens no código
```

### Ambiente virtual e dependências (terminal)

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install pandas pyarrow requests
pip freeze > requirements.txt      # registra as versões instaladas
pip install -r requirements.txt    # recria o ambiente em outra máquina
```

---

## 15. 🧭 Tabela-resumo: "Quero fazer X, uso Y"

| Quero... | Uso |
| :--- | :--- |
| Ler CSV brasileiro | `pd.read_csv(..., sep=";", decimal=",", encoding="utf-8")` |
| Achatar JSON aninhado | `pd.json_normalize()` |
| Ver tipos e nulos | `df.info()` e `df.isna().sum()` |
| Contar valores de uma coluna | `df["col"].value_counts()` |
| Filtrar com várias condições | `df[(cond1) & (cond2)]` ou `df.query("...")` |
| Remover duplicatas pela chave | `df.drop_duplicates(subset=[...], keep="last")` |
| Converter texto em número sem quebrar | `pd.to_numeric(..., errors="coerce")` |
| Criar coluna condicional | `np.where()` ou `np.select()` |
| Criar faixas numéricas | `pd.cut()` |
| Agrupar e agregar | `df.groupby(...).agg(nome=("coluna", "função"))` |
| Total do grupo em cada linha | `groupby(...).transform("sum")` |
| Tabela dinâmica | `df.pivot_table()` |
| JOIN | `df.merge(..., how="left")` |
| UNION ALL | `pd.concat([...], ignore_index=True)` |
| Lista em linhas | `df.explode("coluna")` |
| Média móvel / variação | `.rolling().mean()` / `.pct_change()` |
| Salvar para análise | `df.to_parquet(..., index=False)` |
| Consumir API | `requests.get(..., timeout=30)` + `raise_for_status()` |

---

## 16. 🔁 Equivalência pandas ↔ PySpark

| Operação | pandas | PySpark |
| :--- | :--- | :--- |
| Ler CSV | `pd.read_csv(caminho)` | `spark.read.csv(caminho, header=True)` |
| Primeiras linhas | `df.head()` | `df.show()` / `display(df)` |
| Esquema | `df.dtypes` / `df.info()` | `df.printSchema()` |
| Selecionar colunas | `df[["a", "b"]]` | `df.select("a", "b")` |
| Filtrar | `df[df["v"] > 10]` | `df.filter(F.col("v") > 10)` |
| Nova coluna | `df["n"] = df["v"] * 2` | `df.withColumn("n", F.col("v") * 2)` |
| Renomear | `df.rename(columns={"a": "b"})` | `df.withColumnRenamed("a", "b")` |
| Condicional | `np.where(cond, x, y)` | `F.when(cond, x).otherwise(y)` |
| Agrupar | `df.groupby("k").agg(...)` | `df.groupBy("k").agg(...)` |
| JOIN | `df.merge(df2, on="k")` | `df.join(df2, on="k")` |
| UNION | `pd.concat([df1, df2])` | `df1.unionByName(df2)` |
| Remover duplicatas | `df.drop_duplicates(["k"])` | `df.dropDuplicates(["k"])` |
| Preencher nulos | `df.fillna({"v": 0})` | `df.fillna({"v": 0})` |
| Explodir lista | `df.explode("lista")` | `df.select(F.explode("lista"))` |
| Contar linhas | `len(df)` | `df.count()` |

> 🎯 **Diferença principal:** pandas executa **na hora** e roda em **uma máquina** (limitado pela memória). PySpark é **lazy** (só executa numa ação) e **distribuído** entre os workers. Para dados que cabem na memória, pandas é mais simples; para volumes grandes, PySpark.
