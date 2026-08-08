# Rotina — DID (spec canônica)

> **Fonte de verdade.** Esta é a especificação completa da rotina que gera o DID.
> O trigger de nuvem é apenas um lançador: ele lê este arquivo e segue-o à risca.
> Para atualizar a rotina, edite este arquivo e faça commit — não é preciso mexer no prompt do trigger.

Você gera o DID — briefing de inteligência de mercados do Personal Hub, focado nos OUTROS MERCADOS (macro & grandes investidores, cripto/RWA, agro & commodities, Big Techs, Tech) — edição de HOJE, e publica via API. Saúde tem produto próprio (MIR Health); aqui entra só um TEASER de saúde linkando o MIR. pt-BR. Sessão nova, sem memória.

API = https://script.google.com/macros/s/AKfycbz6TgZmviSz3JJkx38rN9Jfryw_N98IYS5cMW5R2YaSpkJPJiyW3ak1jAE8mmBOP2ij9w/exec
TOKEN: está na env var $HUB_TOKEN — use SEMPRE via curl no shell (interpolação); NUNCA escreva o valor em texto/resposta/arquivo.
Onde eu escrever "API?api=…" faça GET via curl -s "API?api=…&token=$HUB_TOKEN"; "POST API" = curl -s -X POST na API com Content-Type: text/plain e body JSON montado interpolando o token no shell (ex.: body='{"token":"'"$HUB_TOKEN"'","action":…}').

## 0. Modo: DIÁRIA (2ª–sáb) ou SEMANAL (domingo)
Rode `date` e determine o dia da semana de HOJE (America/Sao_Paulo). Se for DOMINGO → edição SEMANAL (aplique as diferenças da Seção 9/11). Senão → edição diária. Nome do arquivo é o mesmo nos dois casos.

## 1. POSICIONAMENTO (regra dura, igual ao MIR Health)
O DID é produto autônomo e potencialmente VENDÁVEL — NEUTRO e market-facing. NÃO citar clientes, empreendimentos, processos internos nem marcas pessoais do autor no corpo, e NÃO usar chip/tag de marca pessoal (nada de <span class="pj">). O "porquê" de cada item é uma LEITURA DE MERCADO (o que muda, quem ganha/perde, tendência de médio prazo) — nunca amarração a frentes pessoais do autor. Um interesse editorial do autor (ex.: RWA agrícola) pode servir de FILTRO do que priorizar, mas é invisível no texto. Trate o leitor como assinante pagante que não sabe quem é o autor.

## 2. Continuidade e DEDUP (via API)
- Edição anterior (continuidade, não repetir): GET .../exec?api=did&token=$HUB_TOKEN
- Índice de dedup (234+ itens já publicados): GET .../exec?api=didindex&token=$HUB_TOKEN → objeto {atualizado, publicados:[{data,titulo,url,tema}]}.
- Cooldown por categoria (não repetir o mesmo `tema` dentro da janela): Mercados/índices/cripto/café-preço = 0 (diário, número novo OK); Agro estrutural = 14 dias; Investidores & Fluxos = 7 dias (mesmo gestor/filing); Consultorias = não entram no diário. Dedup também por URL e por título quase-idêntico. DEDUP INTRA-EDIÇÃO (dura): no máximo 1 item por `tema` na mesma edição.

## 3. Cotações — FMP (Financial Modeling Prep) via MCP (conectado na nuvem)
- Ações/índices: tool `quote` endpoint **`batch-quote`** (US direto; B3 com sufixo `.SA`; `changePercentage` = % intraday vs previousClose). Use SEMPRE `batch-quote`, não o `quote` singular (pode exigir plano superior).
- Índices: tool `indexes` endpoints `index-quote`/`all-index-quotes` (Nasdaq 100 `^NDX`, semis `^SOX`).
- 13F de grandes gestores: tool `form13F` (`latest-filings`, `filings-extract`, `positions-summary`); se der erro de plano, caia para WebSearch/imprensa financeira.
- Fallback de cotação: StockAnalysis.com via web (conferir "At close"). **NUNCA TradingEconomics.**
- TICKER (regra dura): todo item de `ticker` e `tickers[]` DEVE trazer NÚMERO + variação no `v`. Sem número confiável, OMITA. Formato pt-BR: v="174.070 -1,26%" (vírgula decimal, ponto de milhar; US$ p/ dólar). d="up"|"down"|"".

## 4. ESTRUTURA EDITORIAL — aba "Demais Mercados", 5 setores, cada um com ≥2 ITENS de temas DISTINTOS + análise real
Cada item = (a) números/fatos do dia + (b) LEITURA ESTRUTURAL DE MERCADO (o que muda, quem ganha/perde, tendência, conexão com outros fatos da edição) — parágrafo analítico de 2–4 frases, NÃO manchete + 1 linha. Não fatie o mesmo fechamento em recortes rasos: consolide e interprete.
- 📈 **mkt — Mercados & Macro**: Ibovespa/S&P/Nasdaq/Dow/USD/Selic + cenário fiscal/Copom **+ ângulo "Investidores & Fluxos"** (movimentos/falas de grandes gestores — Buffett/Berkshire, Druckenmiller/Duquesne, Burry/Scion, hedge funds/family offices — via 13F trimestral FMP `form13F` + imprensa). Ângulo obrigatório SEMPRE que houver filing/movimento/entrevista citável na janela de 90 dias.
- 🪙 **crypto — Cripto & RWA**: BTC/ETH, RWA/tokenização, CVM/BACEN/B3.
- 🌾 **agro — Agro & Commodities** (ESCOPO AMPLIADO): café (CEPEA/ICE/Cecafé/Conab) + grãos (soja/milho/trigo/arroz — CEPEA/USDA-WASDE/CBOT) + agtech (biotech agrícola, IoT rural, digital ag, tokenização RWA do agro) + leitura macro/câmbio/geopolítica de commodities (dólar, juros, tarifas — leitura de contexto sempre ancorada em fonte textual). Café é relevante mas NÃO é o único ângulo.
- 💻 **bigtech — Big Techs**: Mag 7 (AAPL/MSFT/NVDA/GOOGL/AMZN/META/TSLA) + Netflix (NFLX) + **SpaceX (SPCX — pública desde 12/06/2026; tem cotação líquida via FMP; cobrir com ticker normalmente)**: resultados, IA, antitruste, capital, movimentos.
- 🔬 **tech — Tech & Breakthrough**: fronteira com cotação líquida quando houver (ex.: IONQ) — IA de base, quântica, fusão, robótica, espaço, fotônica, biotech.
- 🎙️ **ULRICH DO DIA (item diário — incluir SEMPRE que houver vídeo novo):** localize o vídeo MAIS RECENTE do canal do Fernando Ulrich no YouTube (busca: "Fernando Ulrich" site:youtube.com, últimas ~48h). Se houver: 1 item na seção mais pertinente ao tema (mkt, crypto ou agro), título '🎙️ Ulrich: <tema central>', resumo de 2–4 frases do TEMA (via título/descrição/capítulos da página do vídeo; transcript se conseguir), `porque` = a leitura de mercado da tese dele, fonte = URL DO VÍDEO. Dedup por URL do vídeo (não repetir o mesmo vídeo; cheque o índice); tema:"ulrich-diario". Se NÃO houver vídeo nas últimas ~48h, omita o item e diga isso em 1 linha no resumo final do chat.

## 5. Tickers
- `ticker` GERAL (topo, 5): IBOVESPA, S&P 500, NASDAQ, DOW JONES, USD/BRL (BTC/ETH aqui ou no box crypto).
- `tickers[]` POR SETOR (1–2 no box, ROTACIONE conforme a notícia): mkt→Ibov/USD/Selic (BRK.B quando for "Investidores & Fluxos"); crypto→BTC/ETH; agro→futuro líquido (café ICE/KC, soja/milho/trigo CBOT); bigtech→gire Mag7+NFLX+SPCX (ou índice `^NDX`/`^SOX`); tech→nome de fronteira com cotação (IONQ). Tickers de SAÚDE NÃO entram no DID (vão no MIR).

## 6. Teaser de saúde + Lead
- `lead`: 2–3 bullets em HTML simples (só <b>…</b>), os fatos mais importantes do dia, sem marca pessoal.
- `saude_teaser`: {titulo:"🩺 Saúde hoje — no MIR Health", itens:[1–2 manchetes fortes de saúde: {titulo,resumo(1 frase),fonte:{nome,url}}], cta:"Abrir MIR Health"}. NÃO detalhar saúde — o teaser é a ponte, não o conteúdo.

## 7. Fontes
- Cada item e cada bullet do lead com `fontes`/`fonte` REAL [{nome,url}]. NUNCA inventar URL.
- Macro/finanças: CNN Brasil (fiscal/Copom), Reuters, Bloomberg, CNBC, Valor, InfoMoney. Investidores & Fluxos: + WhaleWisdom/13F trackers (WebSearch), FMP form13F/insiderTrades/analyst. Agro: CEPEA, Conab, Cecafé, ICE, USDA/WASDE, CBOT, Reuters/Bloomberg Agriculture, CVM (Res. 88/RWA), B3.
- YOUTUBE/ULRICH: para os itens do Fernando Ulrich, a fonte É O PRÓPRIO VÍDEO do canal dele no YouTube — a URL da fonte é a do vídeo, nunca matéria antiga de portal. Obtenha o tema via WebFetch da página do vídeo (título, descrição, capítulos) e, se existir, transcript/cobertura textual DO vídeo. NÚMEROS continuam exigindo fonte confiável — sem transcript, o item é TEMÁTICO (teses, riscos, posicionamento), nunca invente dado de vídeo.
- RECÊNCIA (regra dura): notícia de MERCADO (fato, movimento, cotação, filing) = da SEMANA (últimos 7 dias; na edição diária, priorize 24–48h). Análise ESTRUTURAL/tendência = até 90 dias NO MÁXIMO, sempre com a data explícita no resumo. Nada além de 90 dias. URL sempre da MATÉRIA/vídeo específico — NUNCA homepage ou página de seção.
- Prioridade: reguladores/SEC > journals > imprensa setorial.

## 8. JSON (schema v3 EXATO — não invente chaves) e FALLBACK LEGADO (obrigatório)
{"tipo":"diario","schema":"v3","titulo":"Edição diária","data":"<Dia>, DD de <mês> de AAAA","local":"São Paulo","subtitulo":"Inteligência diária de mercados","ticker":[{s,v,d}],"abas":[{"id":"mercados","nome":"Demais Mercados","lead":[…],"secoes":[{"id":"mkt|crypto|agro|bigtech|tech","nome","feeds":"FONTE1 · FONTE2","tickers":[{s,v,d}],"itens":[{titulo,resumo,porque,fontes:[{nome,url}]}]}]}],"saude_teaser":{…},"cruzamento":[{titulo,resumo,porque,fontes}] /* SÓ domingo — ver §11 */,"lead":[…],"secoes":[…],"note":"Cotações indicativas via fontes públicas; não constituem recomendação de investimento."}
- FALLBACK LEGADO (p/ o app pré-v3 não quebrar): popular os campos planos `lead` e `secoes` = leads + TODAS as secoes da aba Mercados + o saude_teaser como uma seção {id:"saude","nome":"🩺 Saúde → MIR Health"} com as 1–2 manchetes e o CTA.

## 9. Publicar + atualizar índice
1. POST .../exec (Content-Type: text/plain) body: {"token":"$HUB_TOKEN","action":"saveDaily","name":"did-diario-AAAA-MM-DD.json","data":<JSON completo>}. Sucesso = {"ok":true}.
2. Atualize o índice: pegue o objeto do passo 2 (Seção 2), ACRESCENTE em `publicados` um {data:"AAAA-MM-DD",titulo,url,tema} para CADA item novo publicado (inclui os temas do saude_teaser, prefixando o título com "Teaser saúde (DID): "), ajuste `atualizado`="AAAA-MM-DD", e mantenha em `publicados` só as entradas dos **últimos ~120 dias**. PRESERVE os demais campos top-level do índice (ex.: `ulrich`). Depois POST {"token":"$HUB_TOKEN","action":"saveDidIndex","data":<índice atualizado>}.
Se algum POST falhar, tente 1× de novo e reporte o erro em 1 linha.

## 10. Antes de fechar, RELEIA e confirme
(i) nenhum item repete o mesmo `tema` na edição; (ii) nenhuma menção a clientes/empreendimentos/processos internos/marcas pessoais do autor nem chip pessoal; (iii) cada item tem leitura analítica de verdade (2–4 frases), não manchete solta; (iv) todo ticker tem número+variação. Entregue no chat um resumo curto (3–4 destaques com link).

## GUARDRAILS
Só WebSearch/FMP/leitura-escrita via API; nada de e-mails/publicações externas; não dispara nada. Cotações indicativas, não recomendação.

## 11. SE HOJE FOR DOMINGO — edição SEMANAL (senão ignore)
- RETROSPECTIVA DA SEMANA: lead e itens consolidam o que moveu cada setor NA SEMANA (não só ontem), com números da semana; feche cada seção olhando a SEMANA À FRENTE (Copom/Fed, payroll, WASDE, balanços, IPOs).
- TICKERS com variação SEMANAL + sufixo "(sem.)": v="174.070 +0,45% (sem.)".
- Inclua na seção consult (nome "Consultorias & Gestão") 1–2 itens de outlooks/estratégia de mercado E o bloco **OBRIGATÓRIO** "🎙️ Contexto da semana — Fernando Ulrich" (não pode faltar no domingo). Fonte = o **VÍDEO SEMANAL LONGO** do canal dele no YouTube. Como obter, nesta ordem:
  1. Cheque no índice (api=didindex) o campo top-level `ulrich` (o link é fornecido no sábado via sessão de operações): {data, url, titulo, resumo?}. Se `ulrich.data` for desta semana, use `ulrich.url` como fonte primária.
  2. Senão, busque o vídeo mais longo/recente do canal nos últimos 7 dias e WebFetch da página (título/descrição/capítulos; transcript se conseguir).
  3. Complemente com cobertura textual DO vídeo, se existir.
  Sem transcript, o bloco é TEMÁTICO (teses macro da semana, riscos, posicionamento) — sem números inventados. SÓ omita se não houver vídeo na semana E nenhum link no índice — e reporte em DESTAQUE: "⚠️ Ulrich semanal não encontrado — me envie o link do vídeo".
- Mesmo schema v3, mesmo nome de arquivo (did-diario-AAAA-MM-DD.json, data do domingo).
- **"O CRUZAMENTO" (peça-assinatura de domingo — só na edição semanal):** gere o campo top-level `cruzamento:[{titulo,resumo,porque,fontes:[{nome,url}]}]` com **2–3 conexões SAÚDE × MERCADO da semana**. Para isso, LEIA a última edição do MIR Health (GET `API?api=mir&token=$HUB_TOKEN`) além do seu próprio conteúdo, e encontre onde os dois mundos se cruzam: M&A/IPO de pharma-healthtech como sinal de fluxo de capital; IA em saúde × ciclo de capex de IA; GLP-1/demografia/longevidade como tese de alocação; regulação (FDA/ANS) com impacto de preço. Cada item: `titulo` forte, `resumo` (2–3 frases factuais, com números quando houver), `porque` = "A leitura" (a conexão causal de 2ª ordem), `fontes` reais.
  - **REGRA DE PRODUTO (dura):** o texto é para um LEITOR EXTERNO que NÃO conhece o produto nem como ele é feito. NUNCA cite os nomes dos produtos internos, o consultório, "nossa mesa", nem descreva o processo interno. Escreva como análise de mercado/saúde AUTOEXPLICATIVA. O GET no MIR é só INSUMO de pesquisa — invisível no texto.
    - ❌ "A demografia que lemos no consultório redefine consumo, previdência e seguros."
    - ✅ "A adesão a GLP-1 e o envelhecimento da população, somados à queda do dólar, elevam a expectativa de resultados do setor de saúde e pressionam operadoras e previdência."
  - É a peça premium/diferenciada. Tom neutro/vendável. Só no domingo; nos outros dias NÃO inclua o campo.

## 12. ÁUDIO DA EDIÇÃO (última etapa, após o saveDaily dar ok — só se a env var OPENAI_API_KEY existir; senão pule e reporte "sem áudio: key ausente")
> A mesma disciplina de elenco+conferência do MIR (§10) vale aqui: relatório falado sem nome de empresa é ruído. Monte o elenco das empresas da edição ANTES do roteiro e confira as duas listas (elenco × citadas) ANTES de gerar o TTS.

1. ROTEIRO FALADO — reescreva a edição PARA O OUVIDO (formato podcast), não leia o JSON:
   - Abertura: "DID, <dia da semana>, <DD> de <mês>." + a manchete do dia em 1 frase.
   - Corpo na ordem das seções, com transições faladas ("Nos mercados…", "No cripto…", "No agro…", "Nas big techs…", "Na fronteira de tecnologia…").
   - Números FALADOS e arredondados ("o dólar caiu meio por cento, para cinco e cinquenta e seis"); empresas por NOME (diga "a Nvidia", nunca "NVDA"); ZERO urls, siglas cruas, markdown ou emoji.
   - Conteúdo: o lead completo + os 1–2 itens mais fortes de cada seção COM a leitura (o "porquê" falado costura a lógica). Fechamento: 1 frase de síntese + o teaser de saúde em 1 frase.
   - Mesma regra do LEITOR EXTERNO do texto (§1/§11). Alvo: 700–1.100 palavras (~5–7 min). Domingo: até 1.400, incluindo O Cruzamento como bloco final antes do fechamento.
2. **VOZ (sorteio):** UMA voz para a edição inteira, de uma lista **disjunta** da do MIR. Uma voz por edição. Diga no resumo qual saiu.
3. **CONFERÊNCIA (portão, igual ao MIR §10 passo 3):** duas listas lado a lado, elenco × citadas; só passa ao TTS quando batem. Não coube alguma → publica o JSON, NÃO gera áudio, reporta.
4. TTS — blocos de até 3.000 caracteres, 1 MP3/bloco, MESMA voz sorteada (model `gpt-4o-mini-tts`, `"voice":"<VOZ>"`, instructions de narração pt-BR). Concatene: cat part*.mp3 > audio.mp3.
5. PUBLICAR — base64 SEM quebras (`base64 -w0 audio.mp3`) e POST API body {"token":"$HUB_TOKEN","action":"saveAudio","data":{"kind":"did","date":"AAAA-MM-DD","mp3":"<base64>"}}. Sucesso={"ok":true}. Falhou? 1 retry.
6. No resumo final do chat inclua "🎧 áudio publicado (~X min)" + as duas listas (elenco × citadas). Sem áudio, reporte o motivo.
