# Comandos do Git Utilizados e Seus Significados
Estes foram os comandos exatos que usamos para conectar o seu código local à nuvem do GitHub:

git init

O que faz: Inicializa um repositório Git zerado na pasta do seu projeto. É como dizer ao sistema: "A partir de agora, comece a rastrear todas as alterações feitas nestes arquivos."

git add .

O que faz: O ponto (.) significa "tudo". Este comando adiciona todos os arquivos novos e modificados (que não estão bloqueados no .gitignore) ao "palco" (staging area), preparando-os para serem salvos.

git commit -m "Sua mensagem aqui"

O que faz: Tira uma "fotografia" permanente do estado atual dos arquivos que estão no palco. A mensagem entre aspas serve para documentar o que foi feito naquela etapa do trabalho (ex: "Primeiro commit: Pipeline de dados").

git branch -M main

O que faz: Renomeia a ramificação principal do seu projeto para main. É o padrão atual de mercado exigido pelo GitHub.

git remote add origin https://github.com/...

O que faz: Cria a ponte entre o seu computador e o servidor do GitHub. Ele diz ao Git local que existe um repositório remoto (na nuvem) e dá a ele o apelido padrão de origin.

git push -u origin main

O que faz: Faz o envio (upload) efetivo de todos os seus commits locais para o GitHub. A flag -u cria um vínculo, permitindo que, nos próximos envios, você precise digitar apenas git push.


## 📁 1. Gerenciamento de Pastas e Arquivos

O Git monitora apenas arquivos, não pastas vazias. Para estruturar diretórios no repositório, utilizamos estratégias específicas.

### Criar uma pasta e subir para o GitHub
Para garantir que uma nova pasta seja monitorada, adicionamos um arquivo oculto (geralmente chamado `.keep` ou `.gitkeep`) dentro dela.
```bash
mkdir nome-da-pasta          # Cria a pasta localmente
cd nome-da-pasta             # Entra na pasta
touch .keep                  # Cria o arquivo oculto de marcação
git add .                    # Prepara as alterações
git commit -m "feat: estrutura a pasta nome-da-pasta"
git push origin main         # Envia para o repositório remoto

## Mover arquivos preservando o histórico (git mv)
Mover arquivos manualmente pode quebrar o histórico de commits do arquivo. O comando git mv move o arquivo e já avisa o Git sobre a alteração de caminho.
git mv caminho/atual/arquivo.ext novo/caminho/arquivo.ext
git commit -m "refactor: move arquivo para o novo diretório"
git push

## Fluxo Diário de Atualização (O Ciclo Padrão)

git add .
git commit -m "feat: descrição curta e clara da alteração"
git push

- Dica: Use git status antes de adicionar os arquivos para verificar exatamente o que foi modificado na sua árvore de trabalho.

## Segurança e Tratamento de Dados Sensíveis
Caso arquivos de credenciais (como chaves de Service Accounts .json, tokens ou arquivos .env) sejam incluídos por acidente em um commit, o GitHub bloqueará o push por regras de Secret Scanning. Para corrigir sem perder o código desenvolvido, segue o protocolo de contenção:

Passo 1: Configurar o .gitignore
Crie ou edite o arquivo .gitignore na raiz do projeto para garantir que o arquivo sensível não seja rastreado novamente.
# Ignorar arquivos de credenciais e chaves do Google Cloud
*.json
.env
## Passo 2: Desfazer o commit local (Sem perder o código)
Para retirar o arquivo do histórico do último commit mantendo as linhas de código que você acabou de escrever:
git reset --soft HEAD~1

- (O parâmetro --soft mantém as alterações dos arquivos intactas na sua área de trabalho, prontas para serem editadas).

## Passo 3: Remover o arquivo sensível da fila de envio (Staging)
git restore --staged nome-do-arquivo-sensivel.json

- (Agora o arquivo JSON voltará a ficar "vermelho" no git status e será ignorado pelo .gitignore que você configurou no Passo 1).

## Passo 4: Refazer o envio com segurança
git add .
git commit -m "security: configura gitignore e remove arquivos sensíveis"
git push
