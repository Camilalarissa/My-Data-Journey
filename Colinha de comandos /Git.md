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
