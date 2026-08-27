# Relatório — Parada Cooling Medium (FPSO Forte)

Relatório visual de 1 página para acompanhar a parada programada de manutenção com foco em Cooling Medium, emitido 2x ao dia (08:00 e 20:00).

É um app 100% estático (HTML + CSS + JS, sem instalação, sem servidor): abra o arquivo direto no navegador.

## Passo a passo por emissão

1. Dê duplo clique em `relatorio_parada_cooling_medium.html` para abrir no navegador (Chrome ou Edge recomendados).
2. A página abre em modo **Editar**. Duas opções para começar:
   - **Carregar emissão anterior**: seleciona o último arquivo salvo em `dados/` (ex.: a emissão da noite anterior) e pré-preenche o formulário — só ajuste o que mudou.
   - **Novo em branco**: começa do zero.
   - **Carregar exemplo**: carrega os dados de demonstração, útil para testar o layout.
3. Preencha/ajuste os campos: cabeçalho, POB, avanço da parada (datas, % previsto/realizado, série do gráfico por dia/atividade), reparos (agrupados por tipo, com % de Preparação e % de TA por item), atividades realizadas, lookahead (próximas atividades a realizar), riscos, andamento de andaimes (Acesso e Escoramento, cada um com seu próprio % previsto/realizado) e tabela de custos. Use os botões **+ Adicionar** / **✕** para incluir ou remover grupos e itens das listas.
4. Clique em **Visualizar relatório** para ver o layout final de 1 página.
5. Clique em **Salvar JSON**:
   - No Chrome/Edge, escolha salvar diretamente dentro da pasta `dados/` com o nome sugerido (`AAAA-MM-DD-manha.json` ou `AAAA-MM-DD-noite.json`).
   - Em outros navegadores, o arquivo é baixado normalmente — mova-o manualmente para `parada-cooling-medium/dados/`.
6. Ainda na tela de Visualizar, clique em **Imprimir / PDF** (ou Ctrl+P), escolha "Salvar como PDF", papel **A4 paisagem**, sem cabeçalhos/rodapés do navegador. O relatório é desenhado para caber em exatamente 1 página.
7. Envie o PDF gerado aos gerentes.
8. Suba o JSON da emissão para o repositório (isso cria o histórico de todas as emissões):

   ```bash
   git add parada-cooling-medium/dados/AAAA-MM-DD-turno.json
   git commit -m "Emissão parada Cooling Medium — AAAA-MM-DD turno"
   git push
   ```

## Estrutura

```
parada-cooling-medium/
├── relatorio_parada_cooling_medium.html   # app (Editor + Visualizador)
├── dados/                                  # um arquivo JSON por emissão (histórico)
│   └── exemplo-2026-08-26-manha.json
├── assets/
│   └── logo-prio.svg                       # ícone oficial da PRIO (extraído do vetor original)
└── README.md
```

## Layout do relatório

```
┌───────────────────────────────────────────────────────────┐
│ Cabeçalho: logo · dados da emissão · legendas · POB        │
├─────────────────┬──────────────────┬───────────────────────┤
│ Reparos          │ Atividades       │ ┌───────────────────┐ │
│ (Preparação x TA)│ realizadas       │ │ % geral da parada   │ │
├─────────────────┤──────────────────┤ │ (caixa em destaque) │ │
│ Principais riscos│ Lookahead        │ └───────────────────┘ │
│ da pré-parada    │                  │ Evolução por dia        │
├─────────────────┼──────────────────┤ (gráfico Previsto x      │
│ Andamento andaimes│ Tabela de custos│ Realizado)                │
└─────────────────┴──────────────────┴───────────────────────────┘
```

- **% geral da parada** (topo do card "Avanço da parada", coluna direita): o KPI mais importante do relatório fica numa caixa destacada (fundo colorido, números grandes) logo no topo desse card — % físico concluído do cronograma inteiro da parada (todos os reparos somados) + indicador de ritmo (Atrasado/Acima do plano/No plano). Abaixo dela fica o gráfico "Evolução por dia", que detalha dia a dia o que compõe esse número. Não é calculado automaticamente a partir da tabela de Reparos — é preenchido à parte no formulário ("Avanço da parada"), pois representa o cronograma geral da parada, que pode incluir atividades além dos reparos listados.
- **Reparos — Preparação x TA** (coluna esquerda, topo): visão detalhada — % de Preparação e % de execução durante o TA, item a item, agrupado por tipo de reparo.
- **Atividades realizadas** (coluna central, topo) e **Lookahead** (coluna central, embaixo): listas simples (texto + status), sem %, para o que já foi feito e o que vem a seguir — itens que ainda não viraram (ou não precisam virar) um item detalhado na tabela de Reparos.
- **Principais riscos da pré-parada** (coluna esquerda, embaixo de Reparos): lista simples (texto + status).

## Cor das barras Previsto x Realizado

Nos gráficos de barra (Avanço da parada por dia, e Andamento de andaimes), a barra do **Realizado** muda de cor sozinha, sem precisar preencher status manualmente:
- **Verde**: Realizado ≥ Previsto (no plano ou à frente).
- **Vermelho**: Realizado < Previsto (atrasado).

A barra do **Previsto** continua sempre num tom neutro (roxo claro), servindo de referência/meta por trás da barra colorida.

## Notas

- Cada arquivo em `dados/` é autossuficiente (contém todos os campos do relatório daquela emissão) — não depende de nenhum outro arquivo.
- O processo de emissão é manual: não há automação de horário, envio de e-mail ou geração automática de PDF. Alguém do turno preenche e exporta às 08:00/20:00.
- A tabela de custos vem com categorias de exemplo (mão de obra, materiais, equipamentos, terceiros, outros) — ajuste livremente pelo formulário para o plano de contas real.
- Se as listas (riscos, atividades, custos etc.) ficarem muito longas, o relatório reduz automaticamente o tamanho da fonte para tentar manter tudo em 1 página; ainda assim, evite listas muito extensas para manter a leitura confortável.
- As cores de fundo (barras de progresso, bolinhas de status, cabeçalhos de tabela, indicador "ATRASADO") aparecem no PDF **independente de marcar ou não "Imprimir gráficos de segundo plano"** na caixa de diálogo de impressão do navegador — isso é forçado via CSS (`print-color-adjust`), validado gerando o PDF nos dois cenários.

## Identidade visual

O relatório usa a paleta roxo/laranja e a fonte Epilogue da skill de marca `prio-pptx-layout` (originalmente feita para decks PowerPoint — essa skill aponta uma skill irmã, `prio-rs-layout`, com paleta verde neon para HTML/relatórios, que não estava disponível neste momento; a aplicação do roxo/laranja aqui foi uma decisão explícita do usuário).

- **Cores de status** (bolinhas verde/amarelo/vermelho, indicador de ritmo Atrasado/Acima do plano/No plano) foram mantidas com o significado semântico de semáforo, independente da marca — trocar essas cores reduziria a legibilidade operacional do relatório.
- A fonte Epilogue é carregada via Google Fonts; sem internet no momento de abrir o arquivo, o navegador usa automaticamente a fonte de fallback (Inter/Segoe UI) sem quebrar o layout.
- O ícone do polvo (`assets/logo-prio.svg`, cor "navy" `#2b2f53`, uma das variantes oficiais da marca) foi extraído com fidelidade do vetor original enviado pelo usuário — não é mais uma recriação aproximada. O mesmo path está replicado inline no HTML (constante `LOGO_SVG`, dentro de `relatorio_parada_cooling_medium.html`) para não depender de um arquivo externo; ao trocar o logo, atualize os dois.
