# SMPC Report — Projeto de Síntese da Conferência

## Contexto
Registro fotográfico e documental da participação de Guilherme (e trechos com Joseph)
na conferência SMPC 2026, em Evanston, ao longo de 3 dias, incluindo atividades
extra (turismo/cidade) intercaladas com os dias de conferência.

Objetivo final: apresentação em HTML, em ordem cronológica, para compartilhar com
Joseph, que vai adicionar seus próprios comentários antes da apresentação conjunta.

## Estrutura de dados

- `raw/fotos-slides/` — fotos de slides de palestras assistidas na conferência
- `raw/fotos-atividadesExtra/` — fotos de atividades fora da conferência (cidade,
  Guilherme e Joseph)
- `raw/anotacoes/` — PDFs escaneados do caderno (FORA DE ESCOPO nesta rodada —
  não ler, não processar)
- `raw/planilha-palestras.xlsx` — grade de palestras; células amarelas = palestra
  efetivamente assistida por Guilherme
- `raw/Joseph-fotos/` — **AINDA NÃO CRIADA nesta rodada.** Quando existir, conterá
  fotos enviadas por Joseph (slides e/ou atividades extra). O agente deve:
  - tratá-la como fonte adicional, não substituta, das pastas de Guilherme
  - classificar cada foto (palestra vs. atividade extra) por metadata EXIF
    (timestamp), do mesmo modo que as fotos de Guilherme
  - integrar essas fotos na mesma linha do tempo cronológica única, identificando
    a origem (Guilherme/Joseph) de cada item
  - se a pasta ainda não existir, simplesmente ignorar esta fonte sem erro

## Regras de reconstrução cronológica

1. Extrair metadata EXIF (data/hora de captura) de todas as fotos disponíveis em
   `fotos-slides/`, `fotos-atividadesExtra/` e, quando existir, `Joseph-fotos/`.
2. Cruzar horário das fotos-slides com a planilha para identificar a qual
   palestra/sessão cada foto pertence (dia, horário, título, palestrante).
3. Ordem cronológica final intercalando os tipos, por timestamp real —
   NÃO assumir um padrão fixo tipo "atividade > dia > atividade"; seguir o
   relógio.
4. Nem toda palestra marcada em amarelo terá foto correspondente, e pode haver
   janelas sem nenhuma foto