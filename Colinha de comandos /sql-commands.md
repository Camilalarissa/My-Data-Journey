# Guia de Referência Rápida: Comandos SQL e Suas Funções

Este repositório serve como um guia prático dos comandos essenciais da linguagem SQL (Structured Query Language), organizados por suas respectivas sublinguagens.

---

## 1. DQL (Data Query Language) — Linguagem de Consulta de Dados
Usada exclusivamente para recuperar e consultar dados armazenados no banco de dados.

*   `SELECT`: Especifica quais colunas você deseja retornar na consulta.
*   `FROM`: Indica a tabela de onde os dados serão extraídos.
*   `WHERE`: Filtra os registros com base em condições específicas.
*   `GROUP BY`: Agrupa linhas que têm os mesmos valores em colunas específicas (essencial para funções de agregação).
*   `HAVING`: Filtra os grupos gerados pelo `GROUP BY` com base em uma condição.
*   `ORDER BY`: Ordena o resultado da consulta de forma ascendente (`ASC`) ou descendente (`DESC`).
*   `JOIN` (`INNER`, `LEFT`, `RIGHT`, `FULL`): Une tabelas diferentes com base em uma coluna de relacionamento.

---

## 2. DML (Data Manipulation Language) — Linguagem de Manipulação de Dados
Usada para modificar, inserir, deletar e atualizar os dados dentro das tabelas.

*   `INSERT INTO`: Insere novos registros/linhas em uma tabela.
*   `UPDATE`: Modifica dados já existentes em uma tabela (sempre use com `WHERE` para não alterar a tabela inteira).
*   `DELETE`: Remove linhas de uma tabela (sempre use com `WHERE` para não apagar todos os registros).

---

## 3. DDL (Data Definition Language) — Linguagem de Definição de Dados
Usada para definir, alterar ou excluir a estrutura do banco de dados (tabelas, índices, visualizações).

*   `CREATE DATABASE`: Cria um novo banco de dados.
*   `CREATE TABLE`: Cria uma nova tabela dentro do banco de dados.
*   `ALTER TABLE`: Altera a estrutura de uma tabela existente (adicionar, excluir ou modificar colunas).
*   `DROP TABLE`: Exclui permanentemente uma tabela e todos os seus dados do banco de dados.
*   `TRUNCATE TABLE`: Remove todos os registros de uma tabela, mas mantém a sua estrutura intacta para novos dados.
*   `CREATE INDEX`: Cria um índice em uma tabela para acelerar a velocidade de busca e consultas.

---

## 4. DTL / TCL (Data Transaction Language) — Linguagem de Transação de Dados
Usada para gerenciar as transações no banco de dados, garantindo a integridade dos dados (propriedades ACID).

*   `START TRANSACTION` / `BEGIN`: Inicia uma nova transação.
*   `COMMIT`: Salva permanentemente todas as alterações feitas durante a transação atual.
*   `ROLLBACK`: Desfaz todas as alterações feitas na transação atual caso ocorra algum erro, voltando ao estado anterior.
*   `SAVEPOINT`: Cria um "ponto de restauração" dentro de uma transação, permitindo reverter apenas parte dela se necessário.

---

## 5. DCL (Data Control Language) — Linguagem de Controle de Dados
Usada para gerenciar as permissões de acesso dos usuários ao banco de dados (crucial para segurança da informação).

*   `GRANT`: Concede permissões de acesso específicas (como leitura, escrita ou administração) a um usuário.
*   `REVOKE`: Remove ou revoga as permissões que foram concedidas anteriormente a um usuário.

---

## Funções de Agregação Comuns
Funções matemáticas utilizadas frequentemente junto com o comando `SELECT` e `GROUP BY`:

*   `COUNT()`: Conta o número total de linhas retornadas.
*   `SUM()`: Soma os valores de uma coluna numérica.
*   `AVG()`: Calcula a média dos valores de uma coluna numérica.
*   `MIN()`: Retorna o menor valor de uma coluna.
*   `MAX()`: Retorna o maior valor de uma coluna.
