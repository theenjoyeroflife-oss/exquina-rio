# Exquina Rio: instruções do projeto

Agenda de rodas de samba, choro, jazz, bossa, forró, pagode e karaokê em bares e ruas do Rio, organizada por dia. O dono é o Lucas. Fale com ele em português do Brasil, de forma curta e direta.

## Onde o site vive

- **Site público:** `index.html` neste repositório, publicado pelo GitHub Pages em https://theenjoyeroflife-oss.github.io/esquina-rio/. O nome do site é "Exquina Rio", mas o repositório se chama `esquina-rio`.
- **Agenda no Claude:** artifact https://claude.ai/artifact/2jzpheaB5iiGa18xDEdG5J. É uma cópia antiga do site, e as duas versões já divergem:
  - o artifact continua com o mapa esquemático em SVG, porque páginas publicadas no Claude bloqueiam mapas externos;
  - os lugares adicionados em 18/10/2026 só estão no site.

  Quando mudar algo que deva aparecer nos dois, aplique no `index.html` e, se fizer sentido, no artifact (leia antes com `Artifact` action `read` e publique sem o esqueleto `<html><head>…<body>`).
- O site é um arquivo só (HTML, CSS e JS inline). Não há build nem dependências locais.
- **Exquina Show:** `show/index.html`, publicado em https://exquinario.com/show/. Site irmão para bailes funk e charme, shows, festas eletrônicas e feiras/exposições, criado em 04/11/2026 a partir de uma cópia do `index.html`.
  - É um arquivo independente: mudanças no `index.html` não passam para ele sozinhas.
  - Tema noturno (rosa como cor de destaque), sem as abas Saideras e Né segredo, sem preço de cerveja. Abas: Agenda, Mapa, Pra você.
  - Categorias em `CATS`: funk, charme, show, eletronica, festa, cultura. Tipos de lugar em `TIPO`: casa, arena, clube, boate, praca, rua, quadra, centro, lounge, jardim, teatro.
  - Eventos em `E` aceitam `de`/`ate` (AAAA-MM-DD) para limitar o período, usado nas exposições (que aparecem só nos fins de semana para não lotar a agenda).
  - A maior parte da programação está em `D` (datas únicas), com fonte no Tá Sendo (tags funk e eletrônica), Songkick, sites de ingresso e imprensa. Sem horário conferido, use `tbd:1` ("a def." = horário a confirmar no ingresso).
  - Os dois sites têm um link para o outro no cabeçalho (`.irmao`). O AdSense usa os mesmos blocos.
  - **Sempre que atualizar a agenda do Exquina Rio, atualize também a do Exquina Show** (pedido do Lucas em 04/11/2026): tire as datas passadas de `D`, busque eventos dos próximos ~17 dias (Tá Sendo nas tags funk e eletrônica, Songkick, Festas RJ, CCBB, imprensa) e atualize "Datas conferidas em DD/MM" na nota e no rodapé.

## Fluxo de trabalho

- Commit direto na `main` e `git push`, porque o Lucas quer ver as mudanças no ar. Escreva as mensagens de commit em português, no imperativo, por exemplo "Troca…", "Adiciona…", "Corrige…".
- Antes de commitar:
  1. Valide o JS com `node -e` e `new Function(...)` no conteúdo do `<script>`.
  2. Quando a mudança for visual, tire uma captura com Playwright. O Chromium já está instalado, e o script de exemplo fica no scratchpad.
- Este ambiente não acessa Google, cdnjs, CARTO, OpenStreetMap nem o próprio GitHub Pages. Para testar o mapa, intercepte o cdnjs com `page.route` e sirva um stub de Leaflet. Diga ao Lucas o que não deu para ver de verdade.
- Depois de cada mudança, responda em uma ou duas frases: o que mudou e onde já está no ar.

## Dados (no `<script>`)

- `V`: lugares, no formato `{id, n, b (bairro), r: centro|sul|norte|oeste, t: bar|rua|praca|clube|quadra|feira|quintal|laje, a (endereço), lat, lng, ig?}`.
  - Casas sem dia fixo levam `var:"…"`, `cats:[…]` e `src:"chave"` em vez de eventos.
- `SEG`: bares e botecos sem programação, que aparecem na aba "Né segredo" (índice A–Z de todos os bares do site, de `V` e de `SEG`, em cartões de vidro), em Saideras e no mapa (pontos cinza, somem com filtro de estilo ou grátis). Mesmo formato de `V`, com `x` (uma ou duas frases), `src` opcional (chave de `SRC`) e sem eventos.
- Preço da cerveja (`cv`): todo bar ou boteco novo, em `V` ou `SEG`, já entra com `cv:{p, s, m, est?:1}`. Sem preço real, estime pelo tipo de lugar e marque `est:1`. Para botequim de bairro use "R$ 10 a 20", para botequim do Leblon, Ipanema, Lagoa ou Gávea use "R$ 15 a 25", e para bar-restaurante ou casa de música use "R$ 15 a 30", todos com `s:"cvNoite"`. Pedido do Lucas em 30/10/2026.
- `E`: programação fixa, no formato `{id, v (id do lugar), t, c:[cats], d:[0=dom…6=sáb], n?:[semanas do mês], h:"HH:MM", ap?:1 (horário aproximado), p (preço), f?:1 (grátis), ck?:1 ("necessário confirmar"), dur (horas), skip?:["AAAA-MM-DD"], s, x}`.
- `D`: datas avulsas do Instagram, no formato `{id:"d-<lugar>-<MMDD>", dt:"AAAA-MM-DD", …, rep?:[ids de E substituídos], tbd?:1}`.
- `FOTOS`: foto de cada lugar no dropdown, no formato `{<id do lugar>: {src (imagem ~640px), pag (página da foto), cred (crédito)}}`. Use só fotos de licença livre (Wikimedia Commons) ou enviadas/autorizadas pelo Lucas, nunca do Instagram ou do Google. Se a imagem falhar, o bloco some sozinho. Este ambiente não abre o Commons, então as fotos de lá entram sem conferência e o Lucas precisa olhar.
- `s` (fonte) é uma chave do objeto `SRC` ou `"ig:usuario"`. Nunca coloque uma URL direto em `s`: adicione a URL em `SRC` e use a chave.
- Categorias válidas são as de `CATS`: samba, pagode, choro, bossa, jazz, forro, karaoke, rock, feijoada.
- A nota da agenda diz "Atualizado com o Instagram das casas em DD/MM". Atualize essa data sempre que mexer em `D`.

## Regras para os dados

- Use só programação com fonte de 2025 ou 2026. Dia ou horário errado é pior que nenhum dado.
  - Se a fonte é antiga ou agregadora, marque `ck:1`.
  - Se não há dia fixo, use `var`.
- As coordenadas são aproximadas (o site avisa). Se um geocoder não estiver acessível, estime a partir de vizinhos e avise o Lucas.
- Textos em `x` e `var` têm uma ou duas frases, em tom de guia, sem exagero.
- Fontes úteis:
  - O Mapa das Rodas de Samba da Prefeitura (`SRC.mapa`, cerca de 150 rodas).
  - As páginas `tasendo.com.br/festas/<nome>`, que mostram as próximas datas.
  - Sopa Cultural, Time Out Rio, Diário do Rio, Riotur e Sympla.
- Ficaram de fora por falta de fonte recente: Bar Semente, Casa do Choro, Pagode da Tia Doca, Casa Rosa, Bar do Momo, Comuna e Teatro Rival. Bons candidatos para conferir: Pagode do Leão, SZO e Boteco na Rua.

## Visual

- Fontes Big Shoulders Display (títulos) e Instrument Sans (texto). As cores ficam em variáveis em `:root`, com tema escuro.
- Há uma foto de fundo (praia de Ipanema) em base64 dentro do CSS `.fundo`. Ela é a linha gigante do arquivo: não leia nem reescreva essa linha, edite em volta dela.
- A faixa `.calcadao` sob o cabeçalho é uma onda de traço fino com crista enrolada, repetida a cada 80px (máscara SVG com `var(--ink)`). O Lucas rejeitou o padrão preto e branco da calçada de Copacabana.
- O mapa (aba Mapa) usa Leaflet 1.9.4, carregado do cdnjs na primeira visita, com base da CARTO Voyager.
  - Os marcadores são `circleMarker`: azul para lugar com programação, vazado para programação variável, amarelo para o selecionado.
  - O Lucas rejeitou o iframe do Google Maps porque mostrava um ponto só e caía na Glória.

## Chave da CARTO

- O próprio Lucas colocou a chave da CARTO no endereço dos tiles (`M.tiles = L.tileLayer(...?key=...)`).
- Nunca copie a chave para mensagens, commits, docs ou este arquivo.
- Não edite nem reescreva o bloco `M.tiles = L.tileLayer(…)`: commits que tocam essas linhas são bloqueados pela verificação de segurança. Se precisar mudar algo ali, passe ao Lucas o trecho exato com `SUA_CHAVE` no lugar da chave.
- Sobrou uma linha inofensiva, `subdomains:"abcd",`, logo abaixo. O Lucas ficou de apagá-la.

## Atualização diária (tarefa agendada "Atualizar Exquina Rio")

- Roda todo dia às 8h53. Lê o Instagram das casas pelo navegador do app, atualiza `D` e `E` no `index.html`, depois atualiza o `show/index.html`, valida, faz commit e push na `main`.
- Edita os arquivos no lugar. Nunca regenera o `index.html` a partir do artifact.

## Rotina diária (tarefa agendada)

- Todo dia de manhã, uma tarefa confere se o site está no ar e manda ao Lucas, por SendUserMessage e notificação, um resumo curto com 3 a 5 destaques do dia.
  - Prioriza os eventos de `D` e os grátis.
  - Avisa se a data de atualização tiver mais de 8 dias.
- Essa tarefa só lê: não altera o site nem o artifact.
