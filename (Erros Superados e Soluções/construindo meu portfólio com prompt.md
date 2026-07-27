# Minhas anotações — construindo meu portfólio com prompt

- Linha do tempo
Planejei tudo antes (páginas, agentes, stack)
Configurei o ambiente (VS Code + Claude Code)
Fui construindo página por página
Fiz o deploy (GitHub → Render → Vercel)
Ajustes finais (SEO, uns bugs de última hora)
## O que eu acertei 
Planejei antes de codar. Não saí digitando comando. Sentei, pensei na estrutura toda (páginas, agentes, tecnologia) antes de tocar em qualquer código. Isso me poupou de ficar refazendo coisa.
Fui testando bloquinho por bloquinho. Terminei a Home, testei. Terminei Projetos, testei. Nunca deixei acumular várias coisas novas juntas — quando algo quebrava, eu sabia exatamente o que tinha mexido por último.
Usei dados reais cedo. Em vez de deixar tudo com "lorem ipsum" até o fim, coloquei o dashboard real do Looker, o PDF de projeto, meu currículo desde o início. Isso me fez perceber problema de layout na hora (tipo texto cortando porque o título real era maior que o esperado).
Não desisti nos bugs chatos. Venv, cache, configuração da Vercel — nenhum desses era "erro de programação" de verdade, era coisa de ambiente/configuração. Fui atrás até resolver, sem tentar mudar o código à toa achando que era isso.

## Onde eu perdi tempo à toa 
Cache/extensão do navegador me pregou peça DUAS vezes. O texto que sumia no formulário e o chat que não abria — nenhum dos dois era bug real, era só o navegador normal com extensão/cache bagunçado.
→ Anotado: da próxima vez, testar aba anônima ANTES de sair procurando bug no código.
Confundi nome de pasta (venv vs .venv). Perdi um tempo porque o comando de ativar não batia com o nome real da pasta criada.
→ Anotado: sempre confirmar o nome exato da pasta antes de rodar o comando de ativação.
Root Directory da Vercel foi o que mais me deu trabalho. Levei vários prints até descobrir que era isso (framework aparecendo como "Other" em vez de Next.js).
→ Anotado: projeto com frontend e backend juntos (monorepo) SEMPRE vai precisar configurar isso manualmente na hospedagem. Checar isso antes de sofrer com 404.
Quase vazei chave de API em print, duas vezes. Não deu problema porque troquei rápido, mas foi susto.
→ Anotado: antes de printar a tela pra pedir ajuda, dar uma checada rápida se não tem .env aberto em alguma aba.

## Ideias pro próximo projeto 
 Pensar no deploy (monorepo? variáveis de ambiente?) junto com a arquitetura, não só quando for subir o site
 Virar hábito: comportamento estranho na tela → aba anônima primeiro, código depois
 Criar o .gitignore com .env antes até do primeiro commit
 Manter um arquivo tipo decisões.md anotando escolhas técnicas (por que usei tal API, tal serviço) — ajuda a lembrar depois e ajuda quem for mexer no projeto comigo
 Fazer um primeiro deploy "esqueleto" (só a home, sem função nenhuma) logo no começo, só pra testar se a hospedagem está configurada certo — assim os problemas de infraestrutura aparecem no dia 1, não quase no fim
Resumo geral

No fim das contas, o tempo que eu "perdi" foi quase todo em coisa de configuração/ambiente, não em lógica de programação ou arquitetura. Isso é ótimo sinal — quer dizer que meu planejamento lá no início foi sólido. Da próxima vez, é só aplicar essas anotações e o processo fica ainda mais redondo. 🚀
