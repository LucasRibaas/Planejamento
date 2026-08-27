# Relatório — Parada Cooling Medium (FPSO Forte)

Relatório visual de 1 página para acompanhar a parada programada de manutenção com foco em Cooling Medium, emitido 2x ao dia (08:00 e 20:00).

É um app 100% estático (HTML + CSS + JS, sem instalação, sem servidor): abra o arquivo direto no navegador.

## Passo a passo por emissão

1. Dê duplo clique em `relatorio_parada_cooling_medium.html` para abrir no navegador (Chrome ou Edge recomendados).
2. A página abre em modo **Editar**. Duas opções para começar:
   - **Carregar emissão anterior**: seleciona o último arquivo salvo em `dados/` (ex.: a emissão da noite anterior) e pré-preenche o formulário — só ajuste o que mudou.
   - **Novo em branco**: começa do zero.
   - **Carregar exemplo**: carrega os dados de demonstração, útil para testar o layout.
3. Preencha/ajuste os campos: cabeçalho, POB, avanço da parada (datas, % previsto/realizado, série do gráfico por dia/atividade), reparos (agrupados por tipo, com % de Preparação e % de TA por item), lookahead (próximas atividades a realizar), riscos, andamento de andaimes (Acesso e Escoramento, cada um com seu próprio % previsto/realizado) e tabela de custos. Use os botões **+ Adicionar** / **✕** para incluir ou remover grupos e itens das listas.
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
│   └── logo-prio.svg                       # recriação aproximada do mascote (polvo azul) — troque pelo arquivo oficial quando disponível
└── README.md
```

## Dois níveis de avanço (não confundir)

- **Avanço da parada** (quadrante direito): visão macro — % físico concluído do cronograma inteiro da parada, por dia, Previsto x Realizado. É uma curva agregada de todos os reparos somados, útil para ver a tendência geral (atrasado/no plano) rapidamente.
- **Reparos — Preparação x TA** (quadrante esquerdo, parte de cima): visão detalhada — % de Preparação e % de execução durante o TA, item a item, por reparo específico.
- **Lookahead** (quadrante esquerdo, parte de baixo): lista simples (texto + status) das próximas atividades a realizar, sem % — para isso ainda não terem virado um item detalhado na tabela de Reparos.

Os valores de "Avanço da parada" não são calculados automaticamente a partir da tabela de Reparos — são preenchidos à parte no formulário (série do gráfico), pois representam o cronograma geral da parada, que pode incluir atividades além dos reparos listados.

## Notas

- Cada arquivo em `dados/` é autossuficiente (contém todos os campos do relatório daquela emissão) — não depende de nenhum outro arquivo.
- O processo de emissão é manual: não há automação de horário, envio de e-mail ou geração automática de PDF. Alguém do turno preenche e exporta às 08:00/20:00.
- A tabela de custos vem com categorias de exemplo (mão de obra, materiais, equipamentos, terceiros, outros) — ajuste livremente pelo formulário para o plano de contas real.
- Se as listas (riscos, atividades, custos etc.) ficarem muito longas, o relatório reduz automaticamente o tamanho da fonte para tentar manter tudo em 1 página; ainda assim, evite listas muito extensas para manter a leitura confortável.

## Identidade visual

O relatório usa a paleta roxo/laranja e a fonte Epilogue da skill de marca `prio-pptx-layout` (originalmente feita para decks PowerPoint — essa skill aponta uma skill irmã, `prio-rs-layout`, com paleta verde neon para HTML/relatórios, que não estava disponível neste momento; a aplicação do roxo/laranja aqui foi uma decisão explícita do usuário).

- **Cores de status** (bolinhas verde/amarelo/vermelho, indicador de ritmo Atrasado/Acima do plano/No plano) foram mantidas com o significado semântico de semáforo, independente da marca — trocar essas cores reduziria a legibilidade operacional do relatório.
- **Mascote (polvo)** não foi incluído: a própria skill orienta usá-lo só em capas/encerramentos/transições, "nunca competindo com conteúdo técnico denso" — este relatório é justamente um conteúdo técnico denso (tabelas e checklists).
- A fonte Epilogue é carregada via Google Fonts; sem internet no momento de abrir o arquivo, o navegador usa automaticamente a fonte de fallback (Inter/Segoe UI) sem quebrar o layout.
- O mascote atual (`assets/logo-prio.svg`) é uma recriação aproximada, feita a mão a partir de uma referência visual colada no chat — não é um recorte do arquivo oficial (que só foi enviado como imagem inline, sem anexo). Quando o arquivo oficial estiver disponível, substitua `assets/logo-prio.svg` mantendo o nome (ou ajuste o `<img src>` no HTML se usar outro nome/formato).
