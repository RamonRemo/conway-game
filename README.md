# Jogo da Vida

Um Jogo da Vida de Conway feito para rodar em tela cheia como papel de parede vivo.
Um arquivo, 40 KB, zero dependências — abre no navegador e pronto.

**▶ [Jogar](https://ramonremo.github.io/conway-game/)**

```
                         ▄▄
                        ▄  ▄
                       ▄▄▄▄▄
```

---

## O que é

Em 1970 o matemático John Conway inventou um jogo sem jogadores. O tabuleiro é uma
grade infinita de células vivas ou mortas, e tudo obedece a **quatro regras**:

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

## Como usar

| Ação | Como |
|---|---|
| Desenhar | Clique ou arraste no tabuleiro |
| Apagar | Arraste começando de uma célula viva |
| Zoom | Roda do mouse (ancora no cursor) ou o slider **Zoom** |
| Mover a câmera | Shift+arraste, botão do meio ou botão direito |
| Controles | Aproxime o mouse do topo da tela |

No celular: **⋯** no canto abre a barra, um dedo desenha, dois dedos movem e dão pinça.

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
| `0` | centraliza a câmera |
| `F` | tela cheia |

### Botões

- **⬡ Modelo** — solta um dos 33 modelos da galeria num ponto aleatório da tela,
  abrindo uma clareira ao redor para ele nascer limpo
- **⁂ Semear** — limpa tudo e espalha ~60 modelos pelo mundo, bem separados;
  é a sopa aleatória, mas feita de coisas que sabem o que estão fazendo
- **🌈 Change** — a cor das células desliza continuamente, uma volta a cada 2 minutos
- **🎨 Cores** — sorteia fundo e células com contraste garantido

## A galeria

Os 33 modelos, todos com as coordenadas **verificadas por simulação** (veja abaixo):

**Metusaléns** — caos a partir de quase nada
`R-pentomino` (5 células, 1103 gerações) · `Acorn` (7 células viram 633 em 5206) ·
`Rabbits` (17331 gerações) · `Diehard` (some sem deixar rastro em 130) ·
`Thunderbird` · `B-heptomino` · `Pi-heptomino` · `Century`

**Osciladores** — pulsam para sempre
`Blinker` (p2) · `Toad` (p2) · `Beacon` (p2) · `Cross` (p3) · `Pulsar` (p3) ·
`Octagon II` (p5) · `Figure eight` (p8) · `Galáxia de Kok` (p8) · `Pentadecathlon` (p15)

**Naves** — atravessam o mundo
`Glider` · `LWSS` · `MWSS` · `HWSS` (todas c/2) · `Copperhead` (c/10, rara)

**Crescimento infinito** — nunca param
`Gosper Glider Gun` (um glider a cada 30 gerações) · `Crescimento infinito`
(13 células, população cresce para sempre)

**Retas** — o mínimo que rende algo
`Linha 8` · `Linha 25` · `Linha 30`

**Caleidoscópios I–VI** — encontrados por busca
Sopas com simetria de quatro eixos. Como as regras da Vida preservam simetria, a
evolução inteira fica espelhada — de 154 a 498 gerações de caleidoscópio antes de
assentar. Foram achados varrendo 4000 sementes e medindo a longevidade de cada uma.

## Sobre os números

Todo valor de geração citado acima foi **medido**, não copiado de memória. Um
simulador esparso de grid infinito roda cada padrão até a população se tornar
periódica, e o resultado é comparado com o valor canônico conhecido:

```
R-pentomino    estabiliza ~1104  (canônico 1103) ✓
Acorn          estabiliza ~5207  (canônico 5206) ✓
Rabbits        estabiliza ~17332 (canônico 17331) ✓
Diehard        morre na geração 130               ✓
Copperhead     nave de período 10, 28 células     ✓
```

Isso pegou erros reais: `Bunnies` e `Queen Bee Shuttle` deram 14 e 48 gerações em
vez das milhares esperadas — minhas coordenadas estavam erradas, e os dois ficaram
de fora em vez de entrarem quebrados. `Spark coil` e `Herschel` caíram pelo mesmo
motivo.

O padrão de crescimento infinito foi confirmado até a geração 40000: população de
203 → 4540, crescendo linearmente a ~0,11 célula por geração.

## Detalhes técnicos

**Um `index.html`.** Sem build, sem npm, sem CDN. Todo o CSS e JS é inline, e não
há uma única requisição de rede — funciona offline e sobe em qualquer host estático.

**Dois buffers, zero alocação.** O tabuleiro são dois `Uint8Array` fixos; cada
geração escreve no buffer de trás e troca os ponteiros. Nada é alocado no laço, então
não há pressão de garbage collector mesmo a 60 gerações por segundo.

**Mundo finito e toroidal.** O grid tem tamanho fixo (tela ÷ 4 células, ~480×270 em
1080p, 0,25 MB nos dois buffers) e as bordas dão a volta: o que sai pela direita
entra pela esquerda. Isso garante teto de memória — um grid verdadeiramente infinito
cresceria sem limite com um canhão ligado. O custo é fidelidade: metusaléns não
reproduzem a evolução canônica depois que os destroços dão a volta no mundo.

**Câmera separada da simulação.** A simulação não sabe que existe zoom; só o desenho
e o clique consultam a câmera `{x, y, cell}`. O zoom da roda é ancorado no cursor
(converte para coordenada de mundo, escala, recalcula a origem), e a câmera é presa
dentro do mundo para nunca aparecer o tabuleiro repetido.

**Desenho por área visível.** Só as células dentro da tela são desenhadas — aproximar
o zoom acelera o render em vez de pesar.

## Rodando local

```bash
git clone https://github.com/ramonremo/conway-game.git
cd conway-game
xdg-open index.html      # ou simplesmente abra o arquivo no navegador
```

## Licença

MIT.
