
Os Três Grandes Paradigmas do Machine Learning: Supervisionado, Não Supervisionado e por Reforço

Para que um algoritmo consiga extrair inteligência a partir dos dados, ele precisa ser guiado por um paradigma de aprendizado. 
Dependendo do problema de negócio e da estrutura dos dados disponíveis, utilizamos um dos três grandes tipos de aprendizado explicados abaixo.

1. Aprendizado Supervisionado (Supervised Learning):

O Aprendizado Supervisionado é o tipo mais comum no mercado. Ele recebe esse nome porque o algoritmo conta com a ajuda de um "supervisor" — que, na prática, são os dados rotulados (labeled data).
Isso significa que, para cada exemplo enviado durante o treino, o sistema recebe a resposta correta associada (a entrada e a saída desejada).

O objetivo do modelo é aprender a função matemática que mapeia as variáveis de entrada ($X$) para a variável de saída correta ($Y$).

Divisões Clássicas:
Classificação: O objetivo é prever uma categoria discreta (uma classe ou "etiqueta").

Exemplos práticos: Identificar se um e-mail é Spam ou Não Spam; prever se uma transação de compra é Fraude ou Legítima; classificar se um cliente tem alta probabilidade de dar Churn (cancelar o serviço).

egressão: O objetivo é prever um valor numérico contínuo.

Exemplos práticos: Prever o faturamento de uma empresa no próximo trimestre; estimar o preço de um imóvel com base nas suas características; prever o tempo de entrega de um produto no e-commerce.

2. Aprendizado Não Supervisionado (Unsupervised Learning)
No Aprendizado Não Supervisionado, o algoritmo é deixado por conta própria. Ele recebe um conjunto de dados não rotulados, ou seja, o sistema possui apenas as variáveis de entrada, sem nenhuma resposta correta ou alvo (target) definido previamente.

O objetivo aqui não é prever nada, mas sim fazer com que o algoritmo explore a estrutura dos dados para encontrar padrões ocultos, semelhanças ou anomalias.

Divisões Clássicas:
Agrupamento (Clustering): O algoritmo divide os dados em grupos (clusters) com base nas suas características comuns. Itens no mesmo grupo são muito parecidos entre si, mas muito diferentes dos itens dos outros grupos.

Exemplo prático: Segmentação de clientes de um e-commerce para campanhas de marketing (ex: agrupar clientes por padrão de consumo para criar "personas").

Redução de Dimensionalidade: Usado para simplificar dados altamente complexos que possuem dezenas ou centenas de colunas (features), comprimindo as informações essenciais em menos variáveis sem perder os padrões principais (como a técnica PCA).

Associação: Encontrar regras que descrevem grandes volumes de dados.

Exemplo prático: Análise de cesta de compras ("Quem compra fralda também tem 80% de chance de comprar cerveja").

3. Aprendizado por Reforço (Reinforcement Learning)
O Aprendizado por Reforço adota uma abordagem totalmente diferente baseada na psicologia comportamental. Em vez de analisar dados históricos estáticos, o modelo (chamado aqui de Agente) interage ativamente com um Ambiente dinâmico.

O aprendizado acontece por meio de uma dinâmica de tentativa e erro. O agente executa ações no ambiente e, dependendo do resultado dessas ações, recebe um feedback:

Recompensa (Reward): Se a ação o aproximou do objetivo final (ganha pontos positivos).

Punição (Penalty): Se a ação foi errada ou prejudicial (perde pontos).

O objetivo final do agente é desenvolver uma estratégia (chamada de Política) que maximize as recompensas acumuladas ao longo do tempo.

Exemplos Práticos:
Sistemas de Direção Autônoma: Carros inteligentes que aprendem a dirigir sozinhos simulando caminhos, onde manter o carro na pista gera recompensa e colidir gera punição.

Jogos e Competições: Algoritmos como o AlphaGo (da DeepMind) que derrotaram campeões mundiais de jogos complexos jogando milhões de partidas contra si mesmos.

Otimização de Rotas de Entrega: Agentes logísticos que testam milhares de combinações para encontrar o trajeto mais rápido e econômico em tempo real.
