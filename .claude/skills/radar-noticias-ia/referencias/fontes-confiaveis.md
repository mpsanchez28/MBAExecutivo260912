# Fontes confiáveis — catálogo por tier

Este catálogo é **curado e versionado**. Não tente redescobrir fontes confiáveis por
busca: a consulta "principais veículos de jornalismo de IA no Brasil" devolve blogs de
empresa e artigos de SEO, não redações. Se uma fonte boa faltar aqui, acrescente-a
com um commit — não a promova no meio de uma execução.

## ANTES DE TUDO (1): a política de rede do ambiente

Há **dois** bloqueios diferentes, com causas diferentes, e é preciso saber distinguir.

O primeiro é o **proxy de egresso do ambiente**. Se `WebFetch` devolve

> `{"error_type":"EGRESS_BLOCKED", "domain":"...", "message":"Access to ... is blocked by
> the network egress proxy."}`

então a política de rede do ambiente não libera aquele host. Em 24/09/2026 isso foi
verificado em `openai.com`, `agenciabrasil.ebc.com.br` e `www.cnbc.com` — ou seja, era
**geral, não por site**.

Isso é fatal para o radar: sem abrir links, nenhum item passa em V1 e o boletim não pode
ser produzido de forma honesta. **Não contorne.** Não use espelho, cache, AMP nem
agregador para driblar o proxy, e não escreva resumo a partir só do resultado de busca
fingindo que verificou.

O que fazer: registrar `status_execucao: falha` com o motivo, avisar no e-mail, e dizer ao
usuário que o **Network access do ambiente** precisa ser ampliado (menu do ambiente na
barra de título da sessão → Edit → nível de acesso mais amplo, ou os domínios deste
catálogo na lista de permitidos). Os níveis estão descritos em
https://code.claude.com/docs/en/claude-code-on-the-web.

## ANTES DE TUDO (2): o que o crawler não consegue abrir

Vários dos melhores veículos do mundo **bloqueiam o nosso user agent**. `WebSearch` e
`WebFetch` recusam estes domínios com erro explícito:

> `reuters.com` · `apnews.com` · `ft.com` · `wsj.com` · `theverge.com`
> `arstechnica.com` · `wired.com`

Pela regra V1, **link que não abre não entra no registro**. Então esses veículos:

- **não podem ser fonte única** de nenhum item;
- **não contam** como corroboração independente, porque você não leu o que eles disseram;
- podem ser **mencionados em prosa** ("a Reuters apurou que…") **apenas** se o fato estiver
  confirmado por uma fonte que você abriu de verdade — e nesse caso o link do registro é o
  da fonte aberta, nunca o do veículo bloqueado.

Não tente contornar com AMP, cache, espelho ou agregador que reproduz o texto. Se o fato é
real, ele aparece numa fonte primária ou num veículo acessível. Se só existe atrás do
bloqueio, ele vai para "Descartados relevantes" com o motivo `fonte inacessível`.

Esta lista muda. Ao topar com um domínio novo recusado, acrescente-o aqui **no commit do
dia** — é a manutenção mais importante deste arquivo.

## Tier 0 — Fontes primárias

Uma só basta para confirmar o fato. É o documento ou o comunicado original, e com metade
do Tier 1 fora de alcance, é **aqui que a verificação deste agente se sustenta**. Prefira
sempre T0 quando existir.

**Labs e empresas (comunicado oficial)**
`openai.com/index`, `anthropic.com/news`, `deepmind.google/discover/blog`,
`blog.google/technology/ai`, `ai.meta.com/blog`, `blogs.microsoft.com`,
`aws.amazon.com/blogs/machine-learning`, `nvidianews.nvidia.com`, `mistral.ai/news`,
`x.ai/news`, `huggingface.co/blog`, `ibm.com/blog`, newsroom de Salesforce e ServiceNow.

**Pesquisa**
`arxiv.org` (página do abstract, com data de submissão), `nature.com`, `science.org`,
`openreview.net`.

**Regulação e documentos oficiais (internacional)**
`eur-lex.europa.eu` e `digital-strategy.ec.europa.eu` (AI Act), `federalregister.gov`,
`whitehouse.gov`, `nist.gov` / AISI, `gov.uk` / AISI, `sec.gov` EDGAR (8-K, 10-Q, S-1),
`ftc.gov`, `justice.gov`, decisões judiciais em PDF.

**Brasil**
`in.gov.br` (DOU), `gov.br/anpd`, `gov.br/mcti`, `camara.leg.br` e `senado.leg.br`
(PL 2338/2023), `gov.br/cade`, `bcb.gov.br`, `anatel.gov.br`, `gov.br/cvm`.

## Tier 1 — Jornalismo de apuração, acessível

Exigir **2 veículos independentes**, ou **1 + link explícito ao documento primário**
(e nesse caso abra o primário também).

**Internacional — confirmados acessíveis**
CNBC, Axios, MIT Technology Review, The Economist, Bloomberg, The Information,
IEEE Spectrum, Nikkei Asia, SemiAnalysis, Stat News (IA em saúde).

**Brasil — confirmados acessíveis**
Convergência Digital, Teletime, Mobile Time, JOTA (regulatório e jurídico),
Agência Brasil, Núcleo Jornalismo, Brazil Journal, NeoFeed.

**Brasil — bons, mas com paywall frequente**
Valor Econômico, Folha de S.Paulo, O Estado de S. Paulo / Broadcast, O Globo / g1.
Trate pela regra de paywall abaixo.

## Tier 2 — Só como corroboração

Nunca fonte única. Servem para confirmar um fato que já tem T0 ou T1.

TechCrunch, VentureBeat, ZDNet, Engadget,
InfoMoney, Exame, Olhar Digital, Tilt/UOL, Canaltech, Tecnoblog.

## Rejeitar sempre

- Agregadores sem atribuição e content farms de notícias de IA. Sinais: sem byline,
  sem redação identificável, texto que parece gerado, dezenas de posts por dia,
  domínio sem página "sobre" com equipe real. Exemplos encontrados na prática, todos
  no topo de uma busca genérica por "AI news this week": `blog.mean.ceo`,
  `manaknightdigital.com`, `aiagentstore.ai`, `aiweekly.co`, `llm-stats.com`,
  `releasebot.io`, `aireleasetracker.com`, `local-ai-zone.github.io`,
  `aiagentsdirectory.com`.
- Posts de LinkedIn, threads no X/Twitter, Reddit, Medium e Substack pessoal **como fonte
  única**. Valem apenas como pista para rastrear a fonte primária — rastreie e cite a primária.
- Resumos em vídeo e transcrições de YouTube.
- Releases de wire promocional (PRNewswire, Business Wire, GlobeNewswire) sem cobertura
  independente. O release em si pode ser T0 se for o comunicado oficial da empresa
  envolvida — a distinção é essa.
- Republicação de agência sem valor adicionado. Prefira sempre o original.
- **Qualquer URL que você não tenha conseguido abrir nesta execução.**

## Nota sobre paywall

Bloomberg, The Information e os grandes jornais brasileiros costumam ter a notícia boa
atrás do muro. Nesse caso: confirme o fato por fonte aberta, marque `paywall: true`,
cite a fonte paga como corroboração e **não escreva o resumo como se tivesse lido a
matéria**. Se o único acesso é o título, você não verificou nada.
