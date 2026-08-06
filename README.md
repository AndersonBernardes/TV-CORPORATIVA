# TV Corporativa — Guia rápido

## ⚡ Novo layout: cabeçalho + banner + painel + ticker
A tela deixou de ser "um slide por vez, tela cheia" e virou um painel
permanente, dividido em 4 áreas que ficam visíveis ao mesmo tempo:

```
┌─────────────────────────────────────────────┐
│  LOGO                              RELÓGIO   │  ← cabeçalho fixo
├───────────────────────────────┬───────────────┤
│                                │  Cartão 1     │
│      BANNER (rotativo)        │  Cartão 2     │  ← painel lateral
│      70% da largura           │  Cartão 3     │    (cartões fixos,
│                                │  ...          │     todos visíveis)
├───────────────────────────────┴───────────────┤
│  texto rolando •  texto rolando  •  ...        │  ← ticker
└─────────────────────────────────────────────┘
```

Cada slide do `config.json` agora tem um campo opcional `zone` que diz onde
ele mora:
- **`"banner"`** — área grande à esquerda, um slide de cada vez, em rotação
  (igual o sistema antigo: crossfade, animações, tudo continua igual).
- **`"painel"`** — vira um cartão fixo na barra lateral direita. Vários
  cartões ficam visíveis **ao mesmo tempo** (sem rotação), com rolagem se
  não couber tudo.
- **`"ticker"`** — o conteúdo vira texto que rola continuamente no rodapé.

**Se você não especificar `zone`**, cada tipo de slide já tem um destino
padrão sensato:

| Zona | Tipos que vão pra lá por padrão |
|---|---|
| 🖼️ Banner | `image`, `video`, `ad`, `birthday`, `embed`, `text`, `clock` |
| 📋 Painel | `news`, `social`, `menu`, `weather` |
| 📜 Ticker | `motivation`, `rss` |

Pra mudar o padrão de um slide específico, adicione `"zone": "banner"` (ou
`"painel"` / `"ticker"`) nele — ou, mais fácil, use o menu **"Onde aparece na
tela"** no painel admin.

**Cor de destaque:** o cabeçalho e o rodapé usam a cor definida em
`accentColor` no `config.json` (padrão: vermelho `#C0392B`, inspirado na
identidade visual da Anca Soluções). No painel admin, tem um seletor de cor
visual pra isso em **"🏷️ Configurações gerais"** — clique, escolha a cor e
salve, sem precisar editar código nenhum.

## Estrutura de pastas no GitHub
```
seu-repositorio/
├── index.html
├── config.json
└── imagens/
    ├── imagem-01.jpg
    ├── imagem-02.jpg
    └── ...
```

## Publicar
1. Suba estes arquivos para um repositório no GitHub.
2. Vá em **Settings → Pages** e ative o GitHub Pages (branch `main`, pasta `/root`).
3. Você receberá um link tipo `https://seuusuario.github.io/nome-do-repo/`.
4. Configure esse link no navegador em modo kiosk da TV box (ex: Fully Kiosk Browser).

## Atualizar o conteúdo
Você só mexe no `config.json`. Pode editar direto pelo site do GitHub (abra o
arquivo → ícone de lápis → editar → "Commit changes"). Em até
`CONFIG_POLL_SECONDS` (60s por padrão, ajustável no `index.html`) a TV atualiza sozinha,
sem precisar reiniciar nada.


## Tipos de slide disponíveis

### `text` — slide de texto livre
```json
{
  "type": "text",
  "title": "Título grande",
  "body": "Texto de apoio menor",
  "color": "#4FD1C5",
  "fontSize": "90px",
  "bodyColor": "#8B92A0",
  "bodySize": "32px",
  "background": "#1E2128",
  "duration": 8
}
```
- `color` / `fontSize`: cor e tamanho do título (qualquer valor CSS: `#hex`, `red`, `48px`, `5vw`...).
- `bodyColor` / `bodySize`: idem para o texto de apoio.
- `background`: cor de fundo do slide inteiro.
- `duration`: quantos segundos esse slide fica na tela.

### `image` — foto em tela cheia
```json
{
  "type": "image",
  "src": "imagens/imagem-01.jpg",
  "caption": "Legenda opcional",
  "captionColor": "#F2A65A",
  "captionSize": "44px",
  "animation": "zoom",
  "duration": 10
}
```
- `animation: "zoom"` dá um leve efeito Ken Burns (zoom lento). Omita para foto estática.
- `caption` é opcional — se não colocar, a foto aparece limpa.

### `clock` — relógio e data ao vivo
```json
{ "type": "clock", "duration": 8 }
```

### `news` — lista de avisos/notícias
```json
{
  "type": "news",
  "label": "AVISOS INTERNOS",
  "items": ["Aviso 1", "Aviso 2", "Aviso 3"],
  "duration": 12
}
```

## Editando pelo painel visual (admin.html)
Diferente do `index.html` e do `config.json`, o `admin.html` **não precisa ir para
o GitHub**. Guarde ele no seu computador e abra com duplo clique sempre que
quiser editar os slides — ele conversa direto com a API do GitHub usando seu
token, sem depender de estar hospedado em lugar nenhum. Assim só você tem
acesso ao painel (e ao token).

Esse painel é um formulário visual que:
- Lê os slides atuais do GitHub;
- Permite adicionar, reordenar, editar e excluir slides por formulário (sem mexer em JSON);
- Salva direto no GitHub com um clique, usando um token seu.

**Configuração (uma vez só):**
1. Salve o arquivo `admin.html` numa pasta no seu computador e abra ele dando
   duplo clique (abre direto no navegador, como arquivo local).
2. Crie um token em GitHub → Settings → Developer settings → **Fine-grained tokens**,
   com acesso só a este repositório e permissão **Contents: Read and write**.
3. Preencha usuário, repositório, branch e o token no painel, clique em
   "Salvar dados de conexão" (fica salvo só nesse navegador).
4. Clique em "Carregar slides do GitHub" para trazer o que já existe.
5. Edite, adicione ou reordene slides pelos botões e campos.
6. Clique em "Salvar no GitHub" — a TV atualiza sozinha em seguida.

⚠️ Não compartilhe o link do `admin.html` publicamente nem guarde o token em
computador compartilhado, já que ele tem permissão de escrita no repositório.

## Tamanhos de fonte responsivos
Em vez de digitar um valor fixo em pixels, você escolhe entre 4 tamanhos —
**Pequeno, Médio, Grande, Enorme** — tanto no `config.json` quanto no painel
admin. Esses tamanhos se ajustam sozinhos ao tamanho real da tela (TV pequena,
TV grande, monitor), usando uma técnica de CSS chamada `clamp()`. Você não
precisa mais se preocupar em calcular pixels — o texto sempre fica proporcional.

Exemplo no JSON:
```json
{ "type": "text", "title": "Promoção", "fontSize": "grande", ... }
```
Valores aceitos em `fontSize`, `bodySize` e `captionSize`: `pequeno`, `medio`,
`grande`, `enorme`. (Valores antigos em pixels, tipo `"80px"`, continuam
funcionando normalmente, caso você já tenha slides configurados assim.)

## Duplicar slide
No painel admin, o botão **"⧉ Duplicar"** em cada slide cria uma cópia idêntica
logo em seguida — útil pra criar variações rápidas sem preencher tudo de novo.

## Novidades: visual 2026, novos templates e agendamento

### Visual atualizado (tendências de 2026)
- **Preto profundo** (Dark Mode 3.0) — contraste mais nítido em TVs modernas.
- **Vidro fosco (glassmorphism)** nos painéis de legenda, notícias e anúncio.
- **Tipografia cinética** — títulos entram palavra por palavra, em cascata suave.
  Na TV, as animações sempre tocam (ela é uma tela pública, não um dispositivo
  pessoal); só a prévia do painel admin respeita "reduzir movimento" do seu PC.

### Novo tipo: `birthday` (aniversário)
```json
{
  "type": "birthday",
  "title": "Feliz Aniversário, Maria!",
  "message": "Que venham muitas conquistas!",
  "annualDate": "12-25",
  "annualWindowDays": 1,
  "duration": 10
}
```
- `annualDate`: mês-dia (`MM-DD`) — repete todo ano automaticamente, sem precisar editar de novo.
- `annualWindowDays`: quantos dias antes/depois da data ele também aparece (0 = só no dia exato).
- Tem confete animado em CSS puro.

### Novo tipo: `social` (redes sociais)
```json
{
  "type": "social",
  "title": "Siga-nos nas redes sociais",
  "items": ["instagram:@suaempresa", "whatsapp:(11) 99999-9999", "facebook:SuaPagina"],
  "duration": 9
}
```
Prefixos reconhecidos (ganham ícone automático): `instagram`, `facebook`, `tiktok`,
`linkedin`, `whatsapp`, `youtube`, `site`. Qualquer outro prefixo usa um ícone genérico.

### Agendamento — qualquer slide pode ficar oculto ou aparecer só em certas datas
Todos os tipos de slide aceitam estes campos opcionais:
```json
{
  "hidden": false,
  "dateStart": "2026-12-01",
  "dateEnd": "2026-12-31",
  "weekdays": [1,2,3,4,5]
}
```
- `hidden: true` — tira o slide do ar sem precisar apagar ele.
- `dateStart` / `dateEnd` — só aparece dentro desse período (ex: uma campanha de Natal).
- `weekdays` — só aparece nesses dias da semana (0=domingo ... 6=sábado). Deixe vazio pra todo dia.
- No painel admin, isso tudo tem uma seção **"📅 Quando exibir"** em cada slide, com
  campos de data e botões de dia da semana — não precisa editar o JSON à mão.
- A TV reavalia essas regras sozinha a cada minuto, então um slide agendado aparece
  e some na hora certa, mesmo sem ninguém mexer em nada naquele dia.

## Mais opções de animação (imagens e títulos)

### Animações de imagem (campo `animation`)
| Valor | Efeito |
|---|---|
| `zoom` | Zoom lento contínuo (Ken Burns) |
| `pan-left` | Câmera "desliza" da direita pra esquerda |
| `pan-right` | Câmera "desliza" da esquerda pra direita |
| `fade-scale` | Aparece suavemente com leve zoom |
| `blur-focus` | Começa desfocada e ganha foco nítido |
| `slide-in` | Desliza suavemente pra dentro da tela |
| *(vazio)* | Estática, sem animação |

### Animações de título (campo `titleAnimation`, em text/ad/birthday)
| Valor | Efeito |
|---|---|
| `kinetic` (padrão) | Palavras aparecem em cascata, uma após a outra |
| `slideup` | Título inteiro desliza de baixo pra cima |
| `scalepop` | Aparece com um leve "salto" de zoom |
| `glow` | Aparece e depois pulsa um brilho suave continuamente |
| `typewriter` | Efeito de máquina de escrever, letra por letra |
| `none` | Estático, sem animação |

No painel admin, cada slide de texto/anúncio/aniversário agora tem um menu
**"Animação do título"**, e slides de imagem têm o menu **"Animação"** já expandido
com essas opções — tudo por seleção, sem editar JSON.

Na TV (`index.html`), as animações sempre tocam, não importa a configuração do
aparelho — ela é uma tela pública, não o dispositivo pessoal de alguém.

## Novos tipos: previsão do tempo, notícias regionais e site incorporado

### `weather` — previsão do tempo (dados reais, sem precisar de chave de API)
```json
{ "type": "weather", "city": "São Paulo, São Paulo", "lat": -23.5475, "lon": -46.6361, "duration": 8 }
```
- Usa a **Open-Meteo**, um serviço gratuito e sem necessidade de cadastro ou chave.
- No painel admin, digite o nome da cidade e clique em **"🔍 Buscar"** — ele
  encontra as coordenadas sozinho e preenche tudo. Você não precisa saber
  latitude/longitude.
- Mostra ícone do clima, temperatura atual, e máxima/mínima do dia. Atualiza
  sozinho a cada 15 minutos.

### `rss` — notícias regionais via RSS
```json
{ "type": "rss", "label": "NOTÍCIAS REGIONAIS", "feedUrl": "https://g1.globo.com/rss/g1/sp/sao-paulo/", "maxItems": 5, "duration": 12 }
```
- Cole o link RSS de qualquer portal de notícias regional (procure "RSS" no
  rodapé do site, ou pesquise "nome do site + RSS feed"). Muitos portais locais
  brasileiros publicam feeds regionais assim.
- `maxItems`: quantas manchetes mostrar por vez.
- Atualiza sozinho a cada 10 minutos.
- ⚠️ Usa um serviço gratuito de conversão (rss2json.com) que tem um limite
  diário de uso. Pra um único painel de TV isso raramente é um problema, mas
  se notar falhas frequentes, é possível trocar por uma chave própria do
  serviço (gratuita) — me avise se precisar disso.

### `embed` — incorporar um site/dashboard
```json
{ "type": "embed", "url": "https://exemplo.com/dashboard", "duration": 20 }
```
- ⚠️ **Importante:** a maioria dos grandes portais de notícias e redes sociais
  **bloqueia** ser exibida "dentro" de outra página, por segurança (é uma
  proteção do próprio site, não uma limitação daqui). Funciona bem com:
  Google Slides/Sheets (modo "publicar na web"), YouTube, mapas do Google,
  Power BI, Canva, ou qualquer painel que você mesmo controla.
- Pra notícias regionais especificamente, prefira o tipo **`rss`** acima —
  é bem mais confiável, já que a maioria dos portais de notícia bloqueia o
  `embed`.

## Mais polimento visual (crossfade, brilho ambiente, barra de progresso)

- **Crossfade real entre slides**: antes, um slide sumia de repente e o próximo
  aparecia. Agora os dois se misturam suavemente ao mesmo tempo, com um leve
  efeito de "assentar" (zoom sutil de 103% pra 100%) — fica bem mais elegante.
- **Brilho ambiente**: um degradê de luz colorida (nas cores da marca) se move
  bem devagar por trás de cada slide, dando profundidade sem atrapalhar a
  leitura. É sutil de propósito.
- **Barra de progresso nos indicadores**: os pontinhos na base da tela agora
  mostram, em tempo real, quanto falta pro slide atual trocar — no estilo dos
  Stories do Instagram/WhatsApp.

Nenhuma dessas mudanças exige nada novo no `config.json` — é só trocar o
`index.html` no GitHub que tudo já aparece.

## Novos tipos: cardápio, frases motivacionais e vídeos institucionais

### `menu` — cardápio do refeitório (mostra o dia certo sozinho)
```json
{
  "type": "menu",
  "title": "Cardápio do Refeitório",
  "menu": {
    "1": ["Bife acebolado", "Arroz, feijão, purê de batata e salada"],
    "2": ["Almôndegas ao molho", "Arroz, feijão, macarrão e salada"]
  },
  "duration": 10
}
```
- As chaves do `menu` são os dias da semana: `0`=domingo, `1`=segunda, `2`=terça,
  `3`=quarta, `4`=quinta, `5`=sexta, `6`=sábado.
- A TV mostra **só o cardápio de hoje**, automaticamente — não precisa trocar nada.
- Se um dia estiver vazio (ex: fim de semana sem refeitório), o slide **some
  sozinho** nesse dia, sem precisar configurar agendamento manual.
- No painel admin, cada dia da semana tem sua própria lista de itens, editável
  separadamente.

### `motivation` — frases motivacionais (sorteia uma diferente a cada vez)
```json
{
  "type": "motivation",
  "items": [
    "O sucesso é a soma de pequenos esforços repetidos dia após dia.",
    "Grandes equipes constroem grandes resultados.|Anca Soluções"
  ],
  "titleAnimation": "kinetic",
  "duration": 9
}
```
- Cada vez que esse slide aparece, a TV **sorteia uma frase diferente** da
  lista (evita repetir a mesma duas vezes seguidas).
- Formato de cada linha: só a frase, ou `frase|Autor` (autor aparece menor,
  embaixo, se você quiser dar crédito).
- Aceita o campo `titleAnimation` (mesmas opções de outros slides de texto).

**Banco de 30 frases prontas** (copie as que quiser pro seu slide):
```
O sucesso é a soma de pequenos esforços repetidos dia após dia.
Grandes equipes constroem grandes resultados.
Foco no progresso, não na perfeição.
Comece onde você está. Use o que você tem. Faça o que você pode.|Arthur Ashe
Cada dia é uma nova chance de fazer a diferença.
A colaboração transforma metas em conquistas.
Acredite no processo — os resultados vêm com consistência.
Um bom dia começa com uma atitude positiva.
Excelência não é um ato, é um hábito.|Aristóteles
Não espere por oportunidades, crie-as.
O trabalho em equipe divide o esforço e multiplica o resultado.
Pequenos progressos diários somam grandes conquistas.
A confiança se constrói com atitudes, não com palavras.
Aprender é o primeiro passo para evoluir.
Cada desafio é uma chance de crescer.
Simplicidade e consistência vencem perfeição e pressa.
O que fazemos com excelência hoje define quem seremos amanhã.
Ouvir bem é tão importante quanto falar bem.
Compartilhar conhecimento fortalece todo o time.
A qualidade nunca é um acidente; é sempre o resultado de esforço.
Comece devagar, mas nunca pare.
O respeito no dia a dia constrói times fortes.
Cuidar dos detalhes é o que separa o bom do excelente.
A gentileza no ambiente de trabalho também é produtividade.
Cada entrega bem-feita conta uma história de comprometimento.
Organização é a ponte entre metas e resultados.
Ideias simples, bem executadas, mudam tudo.
Feedback é um presente — dê e receba com gratidão.
Motivação começa dentro, mas cresce em equipe.
Hoje é um bom dia para fazer o seu melhor.
```

### `video` — vídeos institucionais (hospedados no seu próprio repositório)
```json
{ "type": "video", "src": "videos/institucional-01.mp4", "muted": true, "loop": false, "duration": 20 }
```
- Crie uma pasta `videos/` no repositório (igual a `imagens/`) e suba seus
  arquivos `.mp4` lá.
- `loop: false` (padrão): o vídeo toca **uma vez** e a TV passa pro próximo
  slide sozinha assim que ele termina — a `duration` nesse caso é só uma
  segurança, caso o vídeo não carregue por algum motivo.
- `loop: true`: o vídeo fica repetindo até a `duration` configurada acabar.
- `muted: true` (padrão): necessário pra a maioria dos navegadores permitir
  o vídeo tocar sozinho, sem alguém precisar tocar na tela primeiro.
- ⚠️ **Sobre som:** se desmarcar "Sem som", alguns navegadores/TV boxes podem
  bloquear o áudio automático até haver uma interação manual — depende do
  app usado na TV (o Fully Kiosk Browser, por exemplo, tem uma configuração
  específica pra liberar autoplay com som).
- ⚠️ **Sobre tamanho:** o GitHub tem limite de **100MB por arquivo**, e o
  repositório inteiro deve ficar idealmente abaixo de ~1GB. Comprima os
  vídeos antes de subir (ferramentas gratuitas como HandBrake reduzem bastante
  o tamanho mantendo boa qualidade) — vídeos institucionais costumam ficar
  ótimos entre 15-60 segundos e poucos MB quando bem comprimidos.

## Imagem no cardápio + galeria de modelos prontos

### Imagem no `menu` (cardápio do refeitório)
```json
{
  "type": "menu",
  "title": "Cardápio do Refeitório",
  "image": "imagens/prato-01.jpg",
  "imageAnimation": "pan-left",
  "menu": { "1": ["Bife acebolado", "Arroz, feijão, purê de batata e salada"] },
  "duration": 10
}
```
- `image` é opcional — se não colocar, o cardápio fica como antes (texto centralizado).
- Com imagem, o layout vira uma divisão elegante: foto de um lado, cardápio do
  dia do outro, num cartão com efeito de vidro fosco.
- `imageAnimation` aceita as mesmas opções dos slides de imagem: `zoom`,
  `pan-left`, `pan-right`, `fade-scale`, `blur-focus`, `slide-in`.

### 🎨 Galeria de modelos prontos (no painel admin)
Uma nova seção no topo do painel admin — **"Modelos prontos"** — reúne **todos**
os tipos de slide disponíveis. Não existe mais um botão "+ separado" pra cada
tipo — a criação de qualquer slide agora passa só por essa galeria, com um
clique já criando o slide inteiro configurado (cores, animação, textos de
exemplo), em vez de você montar cada campo do zero:

| Modelo | O que já vem pronto |
|---|---|
| 👋 Boas-vindas | Texto com brilho pulsante, cores da marca |
| 🏷️ Promoção | Imagem com zoom suave e legenda em destaque |
| 📣 Anuncie aqui | Anúncio com animação de "zoom com salto" |
| 🎂 Aniversário | Confete + entrada suave, pronto pra só trocar o nome |
| 💬 Frase do dia | Efeito de máquina de escrever |
| 🍽️ Cardápio + foto | Já vem com imagem e panorâmica configuradas |
| 📱 Redes sociais | Grade de ícones já com Instagram/WhatsApp de exemplo |
| 🌤️ Previsão do tempo | Só falta buscar a cidade |
| 🎬 Vídeo institucional | Tela cheia, toca uma vez e avança sozinho |
| 🕐 Relógio | Hora e data ao vivo |
| 📰 Avisos internos | Lista de avisos que você escreve na hora |
| 🗞️ Notícias regionais | Já com os campos de feed RSS prontos |
| 🖥️ Site incorporado | Já com o campo de link pronto |

Depois de clicar num modelo, ele já aparece selecionado na prévia — é só
ajustar o texto/imagem pro seu conteúdo real e clicar em Salvar.

## Mais animações de imagem + entrada escalonada (stagger)

### Animações de imagem — lista completa (campo `animation`)
| Valor | Efeito |
|---|---|
| `zoom` | Zoom lento contínuo (Ken Burns) |
| `pan-left` / `pan-right` | Câmera "desliza" da direita/esquerda |
| `fade-in` | **Fade In** — aparece suavemente, sem movimento |
| `zoom-in` | **Zoom In** — aumenta de tamanho ao entrar |
| `bounce-in` | **Bounce In** — entra com um pequeno "salto" |
| `scale-in` | **Scale In** — cresce a partir de um ponto pequeno |
| `slide-right` | **Slide In Right** — entra da direita pro centro |
| `slide-in` | Desliza suavemente pra dentro (efeito mais sutil) |
| `blur-focus` | Começa desfocada e ganha foco nítido |
| `fade-scale` | Aparece suavemente com leve zoom |
| `parallax` | **Parallax** — a imagem e a legenda entram em velocidades/tempos diferentes, criando sensação de profundidade |

Todas essas opções já aparecem no menu "Animação" do painel admin, tanto pro
slide de imagem quanto pra imagem do cardápio.

### Stagger — elementos entrando um após o outro
Sempre que um slide mostra uma **lista** — notícias, redes sociais, itens do
cardápio — cada item agora entra com um pequeno atraso em relação ao anterior,
em vez de tudo aparecer de uma vez. Isso é automático, não precisa configurar
nada — já vem assim em: `news`, `rss`, `social` e `menu`.

### Novos modelos prontos na galeria
- **🖼️ Foto institucional** — imagem em tela cheia, sem legenda, com Fade In.
- **🌆 Vitrine com parallax** — imagem com legenda, demonstrando o efeito parallax.

## Imagem própria por dia no cardápio + slides novos no topo da lista

### Cardápio: cada dia com sua própria imagem
```json
{
  "type": "menu",
  "title": "Cardápio do Refeitório",
  "imageAnimation": "pan-left",
  "menu": {
    "1": { "items": ["Bife acebolado", "Arroz, feijão, purê"], "image": "imagens/prato-segunda.jpg" },
    "2": { "items": ["Almôndegas ao molho", "Arroz, feijão, macarrão"], "image": "imagens/prato-terca.jpg" }
  },
  "duration": 10
}
```
- Cada dia agora tem seus **próprios itens E sua própria imagem** — a TV troca
  a foto automaticamente junto com o cardápio, todo dia.
- `imageAnimation` continua sendo um campo só, aplicado a todas as imagens do
  cardápio (não precisa configurar animação dia por dia).
- **Compatibilidade:** se você já tinha um cardápio configurado no formato
  antigo (uma imagem só, compartilhada por todos os dias), ele continua
  funcionando normalmente — e ao abrir esse slide no painel admin, ele é
  convertido automaticamente pro novo formato.

### Novos slides aparecem no topo da lista
Ao clicar num modelo pronto (ou duplicar um slide), ele agora aparece **como
primeiro item**, logo abaixo da galeria de "Modelos prontos" — sem precisar
rolar a página até o final pra encontrar e editar o que você acabou de criar.
A ordem de exibição na TV continua sendo a ordem da lista (você pode reordenar
com os botões ↑↓ a qualquer momento).

## Dicas
- **Ordem dos slides** = ordem no array `slides` do JSON. Para reordenar, só mudar a ordem no arquivo.
- **Adicionar/remover slide**: adicione ou apague um bloco `{ ... }` dentro de `slides`.
- **Fotos novas**: suba em `/imagens` com nome novo (ex: `imagem-51.jpg`) e referencie no `config.json`.
- **Trocar uma foto existente**: pode sobrescrever o mesmo arquivo — o sistema já usa
  parâmetro anti-cache automaticamente, então não precisa renomear.
- A barra inferior (relógio + bolinhas de progresso) fica sempre visível, mostrando qual slide está ativo.
- A página recarrega sozinha a cada 2h (ajustável em `HARD_RELOAD_MINUTES` no `index.html`) para manter a TV estável.
