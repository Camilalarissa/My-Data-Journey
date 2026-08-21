# 🐍 Principais Comandos e Operações em Python

Este repositório reúne uma referência rápida dos comandos, estruturas e funções mais utilizados na linguagem Python, com foco em sintaxe limpa e aplicação prática.

---

## 1. Entrada e Saída (I/O) e Variáveis

| Comando / Função | Descrição e Aplicação | Exemplo |
| :--- | :--- | :--- |
| `print()` | Exibe informações ou resultados no console. | `print("Olá, Mundo!")` |
| `input()` | Captura dados inseridos pelo usuário (retorna sempre uma `string`). | `nome = input("Digite seu nome: ")` |
| `type()` | Retorna o tipo de dado de uma variável (`int`, `str`, `float`, etc.). | `type(42) # <class 'int'>` |
| `int()`, `str()`, `float()` | Converte (faz *casting*) o tipo de dado de uma variável. | `idade = int("25")` |

---

##  2. Estrutura de Dados Nativas

###  Listas (`list`) — Mutáveis e Ordenadas
```python
frutas = ["maçã", "banana", "laranja"]

frutas.append("uva")  # Adiciona um elemento ao final
frutas.remove("banana")  # Remove o primeiro elemento correspondente
frutas.sort()  # Ordena a lista em ordem alfabética/numérica
print(len(frutas))  # Retorna o tamanho total da lista

## Dicionários (dict) — Pares Chave-Valor

usuario = {"nome": "Maria", "idade": 30, "cargo": "Analista"}

print(usuario["nome"])  # Acessa o valor da chave
usuario["email"] = "maria@email.com"  # Adiciona nova chave-valor
chaves = usuario.keys()  # Retorna todas as chaves
valores = usuario.values()  # Retorna todos os valores

## Tuplas (tuple) e Conjuntos (set)

# Tupla: Imutável (não pode ser alterada após criada)
coordenadas = (-23.55, -46.63)

# Set: Coleção não ordenada e sem elementos duplicados
ids_unicos = {101, 102, 103, 101}  # O segundo 101 será ignorado

# 3. Controle de Fluxo (Condicionais e Loops)
## Estruturas Condicionais (if, elif, else)
idade = 18

if idade >= 18:
    print("Maior de idade")
elif idade >= 12:
    print("Adolescente")
else:
    print("Criança")


## Laços de Repetição (for e while)

# Loop for com range (de 0 a 4)
for i in range(5):
    print(f"Número: {i}")

# Loop while com condição de parada
contador = 0
while contador < 3:
    print(contador)
    contador += 1

#  4. Funções e Métodos de Manipulação de Strings
## Funções Nativas
def calcular_media(valores):
    """Calcula e retorna a média aritmética de uma lista de números."""
    return sum(valores) / len(valores)


resultado = calcular_media([10, 20, 30])


## Manipulação de Texto (Strings)
texto = "  Python para Dados  "

texto_limpo = texto.strip()  # Remove espaços extras no início e fim
maiusculo = texto.upper()  # Converte para MAIÚSCULAS
minusculo = texto.lower()  # Converte para minúsculas
novo_texto = texto.replace("Dados", "Engenharia")  # Substitui termos
partes = texto.split()  # Divide a string em uma lista de palavras

# 5. Compreensão de Listas (List Comprehension)
- Forma concisa e performática de criar listas a partir de iteráveis.


numeros = [1, 2, 3, 4, 5, 6]

# Eleva ao quadrado apenas os números pares
pares_quadrado = [x**2 for x in numeros if x % 2 == 0]
# Resultado: [4, 16, 36]

# 6. Tratamento de Erros e Exceções

try:
    resultado = 10 / 0
except ZeroDivisionError:
    print("Erro: Não é possível dividir por zero!")
finally:
    print("Operação finalizada.")
