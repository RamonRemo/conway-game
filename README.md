# Jogo da Vida

Um Jogo da Vida de Conway feito para rodar em tela cheia como papel de parede vivo.
Um arquivo, 67 KB, zero dependências — abre no navegador e pronto.

**▶ [Jogar](https://ramonremo.github.io/conway-game/)**

```
                         ▄▄
                        ▄  ▄
                       ▄▄▄▄▄
```

---

## O que é

Em 1970 o matemático John Conway inventou um jogo sem jogadores. O tabuleiro é uma
grade de células vivas ou mortas, e tudo obedece a **quatro regras**:

| Situação | Resultado |
|---|---|
| Célula viva com menos de 2 vizinhas | morre de solidão |
| Célula viva com 2 ou 3 vizinhas | sobrevive |
| Célula viva com mais de 3 vizinhas | morre de superpopulação |
| Célula morta com exatamente 3 vizinhas | nasce |

Só isso. Dessas quatro linhas emergem naves que atravessam a tela, osciladores que
pulsam para sempre, canhões que disparam sem parar e padrões de sete células que
levam cinco mil gerações para se acalmar. O Jogo da Vida é Turing-completo: dá para
construir um computador inteiro dentro dele.

São **58 modelos** na galeria, do Blinker de 3 células ao Max, que preenche o plano
inteiro com listras.

## Como usar

| Ação | Como |
|---|---|
| Desenhar | Clique ou arraste no tabuleiro |
| Apagar | Arraste começando de uma célula viva |
| Zoom | Roda do mouse, ancorado no cursor |
| Mover a câmera | Shift+arraste, botão do meio ou botão direito |
| Controles | Aproxime o mouse do topo da tela |

No celular: **⋯** no canto abre a barra, um dedo desenha, dois dedos movem e dão
pinça de zoom.

### Atalhos

| Tecla | Ação |
|---|---|
| `Espaço` | play / pause |
| `S` | avança uma geração |
| `R` | sopa aleatória |
| `C` | limpa tudo |
| `M` | insere um modelo aleatório |
| `X` | sorteia as cores |
| `G` | liga/desliga a grade |
| `I` | modo infinito |
| `B` | bordas fechadas |
| `0` | centraliza a câmera |
| `F` | tela cheia |

### Botões

- **⬡ Modelo** — solta um modelo da galeria num ponto aleatório da tela, abrindo uma
  clareira ao redor para ele nascer limpo
- **⁂ Semear** — limpa tudo e povoa o mundo com dezenas de modelos
- **∞ Infinito** — vigia a população e injeta modelos novos sempre que o mundo
  assenta, para a tela nunca parar
- **⊘ Sem passagem** — fecha as bordas: quem cruza o limite deixa de existir, em vez
  de reaparecer do outro lado
- **🌈 Colorir** — a cor das células desliza continuamente, uma volta a cada 2 minutos
  (ligado por padrão)
- **🕯 Idade** — cada célula nasce escura e vai clareando a cada geração que
  sobrevive; o que se move fica sempre escuro e só o que fica parado clareia
- **🌀 Psicodélico** — arco-íris saturado em faixas oblíquas que correm pela tela
- **🎨 Cores** — sorteia fundo e células com contraste garantido
- **Char** — desenha as células com qualquer caractere ou emoji
- **🖼 Imagem** — usa uma imagem do seu computador como célula (fica só no navegador,
  nada é enviado para lugar nenhum)

## A estatística por trás

O que separa "aleatório" de "interessante" aqui é quase todo amostragem. Quatro
técnicas fazem o trabalho:

**Amostragem estratificada com jitter** — no Semear, o mundo é dividido numa grade e
cada célula recebe uma peça em posição sorteada dentro dela. Posições uniformes
parecem certas mas não são: pontos verdadeiramente independentes formam aglomerados
e deixam buracos grandes. A estratificação garante cobertura sem parecer alinhado.
É o mesmo truque da amostragem de pixels em ray tracing.

**Processo de aglomeração (Thomas / Neyman–Scott)** — em 35% das semeaduras o layout
inverte: 3 a 7 pontos-pai, e as peças caem ao redor deles com desvio gaussiano de 14
a 30 células. Cria bairros densos separados por vazio, e o que escapa de um bairro
viaja pelo deserto até colidir com o vizinho.

**Melhor de k (rejection sampling com função de aptidão)** — o botão Aleatório sorteia
6 receitas de sopa, roda cada uma 100 gerações num tabuleiro de teste de 200×120 e usa
no mundo só a que mais fervia. A pontuação é `população + 2 × agitação`, onde agitação
conta quantas células mudaram de estado nas últimas 30 gerações — população sozinha
premiaria um campo entulhado de blocos parados.

O detalhe que faz isso funcionar: os candidatos variam **parâmetros** (densidade de 10%
a 45%, simetria, manchas), não apenas a semente. Sopas da mesma receita num mundo de
130 mil células se comportam quase igual — lei dos grandes números — e escolher entre
elas não adiantaria nada. A busca é no espaço de receitas. Medido, rende 24% acima do
tiro único, por 108 ms de custo.

**Ruído de valor** — metade das sopas tem a densidade modulada por uma grade grossa de
aleatórios interpolada, criando regiões densas e ralas em vez de estática uniforme.

E uma que não é estatística mas é o maior ganho isolado: **cada peça colocada recebe
uma das 8 simetrias do quadrado** (4 rotações × espelhada). Sem isso todo glider do
mundo voa para o mesmo canto.

As dimensões são independentes e se combinam: a **paleta** decide *quais* modelos
(1, 2, 3 ou todos), o **layout** decide *onde* (estratificado ou aglomerado).

## Rodando local

```bash
git clone https://github.com/ramonremo/conway-game.git
cd conway-game
xdg-open index.html      # ou simplesmente abra o arquivo no navegador
```

## Créditos

Padrões da galeria vindos do acervo de [conwaylife.com](https://conwaylife.com),
via o espelho [copy.sh/life](https://copy.sh/life/).

## Licença

MIT.
