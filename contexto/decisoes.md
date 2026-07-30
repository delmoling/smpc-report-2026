# Decisões e casos ambíguos — Etapa 1 (Reconstrução cronológica)

## 10. Auditoria e curadoria visual (Curador Visual), 2026-07-30

Rodada de auditoria de webdesign/UX conduzida pelo agente `curador-visual`
sobre `output/smpc-report.html`, seguida de aplicação das mudanças aprovadas
por Guilherme.

### Técnicas (aplicadas direto, reversíveis via Git)

- **Ken Burns**: amplitude reduzida de `scale(1.08→1.16)` para
  `scale(1.04→1.09)` e duração esticada de 9s para 14s. Motivo: o site não
  tem autoplay/timer (navegação é 100% manual), então a foto pode ficar
  parada no estado final do zoom por tempo indefinido enquanto Joseph
  comenta — amplitude menor e duração maior reduzem o corte residual visível
  quando isso acontece, e evitam amplificar ilegibilidade em fotos de slide
  com texto pequeno.
- **Classe `.no-zoom`** (sem animação, `scale(1.02)` fixo) criada e aplicada
  a uma única foto: `20260722_223205761_iOS.jpg` (sessão "Navegando entre
  duas sessões", 17:00–18:00). Motivo: revisão visual das 40 fotos de slide
  de Guilherme mostrou que só essa tem o miolo do diagrama (labels dentro das
  caixas "Music Perception"/"Auditory Function"/"Cognitive Abilities")
  visivelmente borrado por tremido de câmera — zoom animado pioraria a
  leitura. As outras 39 fotos de slide mantiveram Ken Burns normal (texto
  nítido o suficiente mesmo com zoom).
- **Alt text descritivo**: substituído `alt=""` vazio por
  `${s.title} — foto de ${p.who}, ${s.day} às ${p.t}` em todas as imagens,
  usando campos já existentes no array `sessions` (sem novo campo de dados).
- **`object-position` para retratos**: nova classe `.media.portrait`
  (`object-position:center 38%`), aplicada dinamicamente via JS no evento
  `load` da imagem (`naturalWidth < naturalHeight`). Não foi feito o
  levantamento foto-a-foto de qual `object-position` é ideal para cada
  retrato (ficou como próximo passo se algum corte específico incomodar na
  prática) — `38%` é uma estimativa geral, assumindo que conteúdo relevante
  (tela/palestrante) tende a ficar na metade superior-central do
  enquadramento retrato.
- **Compressão de 11 arquivos >3MB** em `output/images/` (redimensionados
  para 1600px no lado maior, JPEG qualidade 78, `Pillow`/`exif_transpose`):
  redução total de ~36,5MB para ~4,2MB (~88%) nesse conjunto. Backup dos
  originais em resolução total feito em `output/images-fullres-backup/`
  antes de sobrescrever (nunca sobrescrever sem cópia). Arquivos afetados:
  `joseph_20260724_102855.jpg`, `joseph_20260724_094725.jpg`,
  `20260722_132248470_iOS.jpg`, `joseph_20260723_150838.jpg`,
  `joseph_20260724_093856.jpg`, `joseph_20260723_152814.jpg`,
  `joseph_20260723_152903.jpg`, `joseph_20260724_100526.jpg`,
  `20260722_201137484_iOS.jpg`, `joseph_20260723_150440.jpg`,
  `joseph_20260723_145105.jpg`.

### Editoriais (decisão de Guilherme, alteram o array `sessions`)

- **3 fotos órfãs incorporadas** à sessão "Special Session: Mini-Concert &
  Mixed Reality Workshop" (Dia 1, 13:15–14:15):
  `20260722_182057620_iOS.jpg` (13:20), `20260722_182439545_iOS.jpg`
  (13:24), `20260722_182557278_iOS.jpg` (13:25). Motivo: essas fotos de
  Guilherme (headset VR, slide "DreaMR", pianista) estavam em disco mas não
  referenciadas em nenhuma sessão; o texto da sessão afirmava incorretamente
  que "Guilherme não fotografou esta sessão" — o texto foi corrigido
  (removida a frase), já que ele de fato fotografou.
- **Duplicata removida do array**: `joseph_20260722_170350.jpg` (sessão
  "Navegando entre duas sessões", 17:00–18:00, Dia 1). Motivo: mesmo slide
  que `joseph_20260722_170400.jpg` (10s depois), que tem um bullet extra
  revelado no mesmo slide e a mesma nitidez — mantida por ter mais
  informação. O arquivo original **não foi deletado** de `raw/` nem de
  `output/images/`, só removido da referência no HTML.
- **Novo card criado**: "Intervalo livre — Grosse Point Lighthouse" (Dia 1,
  14:15–16:00), com as 2 fotos do farol histórico de Evanston
  (`20260722_201007237_iOS.jpg` 15:10, `20260722_201137484_iOS.jpg` 15:11)
  que estavam em disco sem sessão correspondente — o intervalo 14:15–16:00
  não tinha nenhum card na grade original. Timestamps calculados com o
  offset UTC−5 já documentado no item 1 (nome de arquivo iOS em UTC, EXIF em
  horário local).

### Achado colateral (não alterado)

- Reconfirmado o achado do item 2 (arquivos `.heic` duplicados byte-a-byte
  entre `fotos-slides/` e `fotos-atividadesExtra/`) — nenhuma ação nova
  tomada, segue como decisão de organização pendente para Guilherme.

### Pendências / próximos passos sugeridos (não aplicados nesta rodada)

- Object-position por foto individual (caso `38%` fixo não sirva bem para
  alguma foto específica de pessoas/comida).
- Atualizar Claude.md: `raw/Joseph-fotos/` já existe (67 arquivos), a
  descrição atual do arquivo ainda diz "ainda não criada".

## 1. Fuso horário do EXIF vs. nome do arquivo

Os arquivos iOS nomeiam-se `YYYYMMDD_HHMMSSmmm_iOS` usando o timestamp em **UTC**,
enquanto o EXIF `DateTimeOriginal` embutido está em **horário local** (Evanston,
Central Time, UTC-5 nas datas em questão — confirma horário de verão/CDT).
Ex.: arquivo `20260722_150629138_iOS.heic` → EXIF `2026-07-22 10:06:29`.

**Decisão:** usei o horário EXIF (local) como referência para cruzar com a
planilha, já que a planilha lista horários locais da conferência. O nome do
arquivo foi mantido apenas como identificador, não como fonte de horário.

Isso também gerou uma pequena inconsistência de rotulagem: o arquivo
`fotos-atividadesExtra/20260724_000058662_iOS.heic` tem nome com data 24/07
(por causa do UTC cruzando a meia-noite), mas seu EXIF local é 23/07 19:00.
Tratado como pertencente ao Dia 2 (23/07) na linha do tempo.

## 2. Arquivos duplicados entre `fotos-slides/` e `fotos-atividadesExtra/`

Dois arquivos existem, **byte-a-byte idênticos** (hash MD5 confirmado), nas
duas pastas simultaneamente:

- `20260722_210134022_iOS.heic` (MD5 `98a457766883b9573fbbf859ab294bd2`)
- `20260722_212748883_iOS.heic` (MD5 `b296e4bde481ffccce8b3c261b86dcdc`)

Isso contradiz a premissa do Claude.md de que as pastas são categorias
mutuamente exclusivas (slide de palestra vs. atividade extra).

**Decisão:** tratei ambos como "foto de slide" (pasta `fotos-slides/`) na
linha do tempo, já que seus timestamps caem dentro de blocos de palestra
amarelos (16:00–17:00 do Dia 1). Não removi as cópias de
`fotos-atividadesExtra/` — isso é uma decisão de organização de arquivos que
cabe a Guilherme, não ao agente. **Pergunta em aberto para Guilherme:** essas
fotos foram classificadas erroneamente em uma das pastas, ou é intencional
(ex: uma foto de slide que também mostra contexto da atividade)?

## 3. Fotos em blocos de 1 hora com duas salas amarelas simultâneas

Em três blocos, duas salas foram marcadas em amarelo ao mesmo tempo (ou seja,
Guilherme aparentemente circulou entre duas sessões paralelas), e as fotos
daquele intervalo não trazem nenhuma informação (GPS, pasta, nome) que
permita saber de qual sala vieram:

- **Dia 1, 16:00–17:00**: Regenstein ([T15] Ashley) *e* McClintock ([T40]
  Chu, McNamara) — 6 fotos.
- **Dia 1, 17:00–18:00**: Galvin ([T3] Christianson) *e* Regenstein ([T33]
  Nayak) — 11 fotos.
- **Dia 2, 13:15–14:15**: Regenstein ([T16] Liu) *e* McClintock ([T5]
  Lecamwasam) — 1 foto.
- **Dia 3, 13:15–14:15**: McClintock ([T19] Kathios, Wilson) *e* Regenstein
  ([T25] Wood) — 5 fotos.

**Decisão:** mantive essas fotos associadas ao **bloco de horário** (ambas as
sessões candidatas listadas), sem tentar adivinhar a sala exata ou dividir as
fotos entre as duas sessões. Uma atribuição mais precisa exigiria olhar o
conteúdo das fotos (crachá da sala, slide específico) — fora do escopo desta
etapa, que é puramente cronológica/factual.

## 4. Fotos que caem fora de qualquer janela amarela

Três fotos de `fotos-slides/` têm timestamp dentro de um bloco da planilha,
mas esse bloco **não está marcado em amarelo**:

- Dia 1, 12:40 — `20260722_174029749_iOS.heic` → cai no "Lunch Break / Early
  Career Panel" (11:45–13:15), que não é uma sessão de talks paralela e não
  tem marcação de cor.
- Dia 2, 16:05 — `20260723_210555077_iOS.heic` → cai no "Business Meeting /
  Awards" (15:45–17:00), não marcado em amarelo.
- Dia 2, 17:57 — `20260723_225718450_iOS.heic` → cai no "Keynote Address"
  (17:00–18:00), não marcado em amarelo.

**Decisão:** mantive essas fotos na linha do tempo, associadas ao evento da
planilha correspondente ao horário (mesmo sem marcação amarela), sinalizadas
com ⚠️. Não descartei nem reclassifiquei. **Pergunta em aberto para
Guilherme:** essas fotos são de slides desses eventos (painel de carreira,
business meeting, keynote) que simplesmente não foram marcados em amarelo na
planilha porque não são "talks" formais da grade paralela? Se sim, talvez
devessem contar como "assistido" mesmo sem cor.

## 5. Granularidade da atribuição foto→palestra individual

A planilha só define o horário de início/fim do **bloco** (ex.: 9:45–10:45),
não o horário de cada talk individual dentro do bloco (chair + 2-4
apresentações). Não há como saber, só pelo timestamp da foto, qual
apresentação específica dentro do bloco está sendo fotografada.

**Decisão:** a linha do tempo associa fotos ao **bloco/sessão** inteiro, não
a talks individuais. Onde um bloco teve várias palestras amarelas e várias
fotos, listei todas as palestras do bloco e todas as fotos juntas, sem tentar
casar foto individual → talk individual.

## 6. `raw/Joseph-fotos/`

**Atualização — rodada de integração das fotos de Joseph.** A pasta agora existe,
com 66 fotos (`YYYYMMDD_HHMMSS.jpg.jpeg`) e 1 vídeo (`YYYYMMDD_HHMMSS.mp4`),
reenviadas como "Documento" no WhatsApp.

**Passo 0 — confirmação de que o EXIF sobreviveu ao reenvio:** verificado com
Pillow (`Image._getexif()`) em todos os 66 arquivos `.jpg.jpeg`. Resultado:
**66/66 com EXIF `DateTimeOriginal` legível**, todos do mesmo aparelho
(`Make=samsung`, `Model=Galaxy S24+`, `Software=S926USQS6DZF2`). Ou seja, o
reenvio como "Documento" (em vez de mídia comprimida) preservou o EXIF
original com sucesso — confirmado antes de qualquer classificação.

Para o vídeo `.mp4`, não há EXIF (formato não suporta), mas o container MOV/MP4
carrega `creation_time` (UTC) e uma tag proprietária
`com.samsung.android.utc_offset=-0500`, extraídas via `ffprobe`:
`creation_time=2026-07-22T18:33:58Z`, `utc_offset=-0500` → horário local
2026-07-22 13:33:58 (fim da gravação; duração 15.77s, início ~13:33:41). O
offset -0500 confirma o mesmo fuso (CDT) já usado para as fotos de Guilherme,
então não houve necessidade de nenhum ajuste especial de fuso horário para as
fotos/vídeo de Joseph.

**Particularidade de nomenclatura:** ao contrário dos arquivos iOS de
Guilherme (nome em UTC, EXIF em horário local — ver item 1 acima), os arquivos
do Samsung Galaxy S24+ de Joseph já nomeiam o arquivo com o horário **local**:
o nome bate exatamente com o EXIF `DateTimeOriginal` em todos os 66 casos
(ex.: `20260722_110020.jpg.jpeg` → EXIF `2026-07-22 11:00:20`). Isso foi
confirmado arquivo a arquivo antes de usar o nome como atalho para o horário
nas tabelas da linha do tempo.

**1a — fotos sem timestamp legível:** nenhuma. Todos os 67 arquivos (66 fotos
e 1 vídeo) tinham metadata de horário utilizável. Não há, portanto, nenhuma
entrada na categoria "sem timestamp, requer confirmação manual do Joseph"
nesta rodada.

**Classificação e cruzamento com a linha do tempo:** todas as 66 fotos e o
vídeo de Joseph têm timestamp caindo dentro de um bloco de horário **que já
existia** na linha do tempo (seja bloco amarelo com foto de Guilherme, seja
bloco amarelo que antes estava "sem foto (esperado)"). Não foi necessário
criar nenhum item novo marcado como "registrado apenas por Joseph" — a
cobertura de Joseph, neste lote, é inteiramente complementar à estrutura já
reconstruída na Etapa 1/2. Resumo dos 8 blocos atingidos:

- Dia 1, 10:45–11:45 [T6] Transfer effects of music — 20 fotos adicionais de Joseph (11:00–11:38), mesmo bloco das 3 fotos de Guilherme.
- Dia 1, 13:15–14:15 Special Session Mini-Concert/Mixed Reality Workshop — 2 fotos e 1 vídeo adicionais de Joseph (13:18–13:34). Guilherme não tinha foto aqui; passou de "sem foto" para "com registro (só de Joseph)".
- Dia 1, 16:00–17:00 (parallel T15/T40) — 4 fotos adicionais de Joseph (16:03–16:15), mesma ambiguidade de sala de 2 sessões simultâneas já registrada.
- Dia 1, 17:00–18:00 (parallel T3/T33) — 11 fotos adicionais de Joseph (17:01–17:22), mesma ambiguidade de sala.
- Dia 2, 13:15–14:15 (parallel T16/T5) — 7 fotos adicionais de Joseph (13:17–13:58), mesma ambiguidade de sala.
- Dia 2, 14:15–15:45 Poster Session & Coffee — 14 fotos adicionais de Joseph (14:51–15:29). Guilherme não tinha foto aqui; passou de "sem foto" para "com registro (só de Joseph)".
- Dia 2, 17:00–18:00 Keynote Address (Miriam Lense) — 3 fotos adicionais de Joseph (17:03–17:20), mesmo bloco já sinalizado com ⚠️ pela foto isolada de Guilherme (17:57) em evento não-amarelo.
- Dia 3, 09:00–10:30 Poster Session & Coffee — 5 fotos adicionais de Joseph (09:38–10:28), mesmo bloco das 3 fotos de Guilherme.

**Decisão sobre diferenças de poucos minutos:** em nenhum dos 8 blocos acima o
timestamp de uma foto de Joseph caiu fora da janela do bloco na planilha —
todas caem dentro do intervalo de início/fim do bloco correspondente, então a
regra "diferenças de poucos minutos = mesma sessão, normal" não precisou ser
invocada como julgamento de caso-limite; é aplicada apenas como princípio
geral de leitura das tabelas (fotos podem começar minutos após o início oficial
do bloco).

**Arquivos de imagem gerados:** as 66 fotos foram normalizadas (rotação EXIF
aplicada via `ImageOps.exif_transpose`, já que o Galaxy S24+ grava
`Orientation=6` e precisa da correção, diferente das fotos de Guilherme cujo
processo de conversão HEIC→JPG já normalizou a orientação) e salvas em
`output/images/joseph_<nome-base>.jpg`, prefixadas com `joseph_` para não
colidir com os arquivos de Guilherme e para deixar a origem óbvia no
próprio nome do arquivo.

## 9. Reconstrução visual — layout "Stories/Gallery" v3 (2026-07-29)

A pedido de Guilherme, `output/smpc-report.html` foi refeito por completo a
partir de um protótipo v3 fornecido (estética escura estilo Instagram
Stories, com glassmorphism). A versão anterior (abas por dia + grade de
cards + lightbox, já sem os campos de comentário do Joseph — ver item 8
abaixo) foi arquivada em
`arquivo/2026-07-29/smpc-report-v2-tabs.html`, sem alterações.

A nova versão renderiza cada sessão como um "slide" em tela cheia
(`.ph-layer`, com Ken Burns), navegado por: barra de progresso segmentada no
topo (`.storybar`, um segmento por sessão, clicável), zonas de toque nas
laterais + setas/pontos no centro inferior (`.carouselnav`), menu lateral
deslizante agrupado por dia (`.navpanel`), e uma infobox no canto inferior
esquerdo com dia/tema/horário + sanfona "Ver programação" (`.progpanel`)
para os detalhes de palestrantes — mesma ideia de "informação oficial
disponível sob demanda" da rodada anterior, agora num componente expansível
com o novo visual.

Todo o conteúdo das 24 sessões (pré-conf + 3 dias) e as 118 entradas de
foto/vídeo foram migrados 1:1 do prototype de dados antigo para o novo
formato `{f, who, t, video}` — nenhuma foto foi perdida ou reclassificada
nesta rodada; validado por contagem automática (24 sessões, 118 fotos/vídeo,
5 sessões sem foto, 1 vídeo, todos os arquivos de imagem/vídeo referenciados
conferidos contra `output/images/`). Sessões sem foto (ex.: Welcome &
Opening, Opening Reception) exibem um slide de texto (placeholder) em vez de
imagem, mas ainda ocupam um segmento próprio na storybar.

O vídeo de Joseph (Mini-Concert/Mixed Reality Workshop) foi implementado com
`autoplay muted loop playsinline controls` — inicia em loop silencioso como
os demais slides, mas o usuário pode usar os controles nativos para ouvir o
áudio, mantendo a exigência de "vídeo reproduzível inline" já registrada no
requisito original.

## 8. Mudança de escopo — remoção dos campos "Comentário do Joseph" e reconstrução do HTML (2026-07-29)

A partir desta rodada, `output/smpc-report.html` deixou de coletar comentários
por escrito dentro do documento. Os blocos `<div class="joseph">💬 Comentário
do Joseph: (aguardando)</div>` que existiam em cada card foram removidos por
completo — a apresentação passa a ter um único propósito: mostrar tema +
horário/sala + fotos de cada sessão assistida, como estímulo visual para uma
conversa ao vivo entre Guilherme e Joseph (as observações acontecem na
conversa, não no HTML). Não há mais nenhum campo de texto livre/editável no
documento.

A versão anterior (com os campos de comentário) foi arquivada, sem
alterações, em `arquivo/2026-07-29/smpc-report.html`, conforme convenção do
projeto de nunca deletar nada.

O HTML foi reconstruído do zero como uma experiência de apresentação
interativa (não mais lista de cards em coluna única): navegação por dia
(abas fixas no topo) + navegação por sessão dentro do dia (chips
horizontais), fotos em grade com lightbox (zoom, navegação anterior/próxima,
teclado), vídeo de Joseph reproduzível inline e no lightbox, e a informação
oficial da sessão (palestrantes, títulos das talks) movida para um
`<details>` expansível ("Programação oficial ▸"), fechado por padrão, para
não poluir a visualização principal. Toda a lógica de conteúdo já resolvida
na Etapa 1/2 foi preservada: blocos com duas salas simultâneas ("circulando
entre duas sessões paralelas"), sessões sem cor amarela mas assistidas
(Panel Discussion, Poster Sessions), e o tratamento das duas fotos duplicadas
entre `fotos-slides/` e `fotos-atividadesExtra/` (usadas uma única vez, como
foto de slide).

## 7. Aba "Poster Sessions" da planilha

Verificada — não contém nenhuma célula amarela (nenhum pôster específico
marcado como visitado). Os únicos itens amarelos relacionados a pôsteres são
os blocos "Poster Session & Coffee" na aba "Schedule of Talks" (Dia 2 e Dia
3), tratados como sessão assistida em bloco, não em nível de pôster
individual.
