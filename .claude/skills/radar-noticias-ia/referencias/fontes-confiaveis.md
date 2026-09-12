# Fontes confiáveis — catálogo por tier

Este catálogo é **curado e versionado**. Não tente redescobrir fontes confiáveis por
busca: a consulta "principais veículos de jornalismo de IA no Brasil" devolve blogs de
empresa e artigos de SEO, não redações. Se uma fonte boa faltar aqui, acrescente-a
com um commit — não a promova no meio de uma execução.

## Tier 0 — Fontes primárias

Uma só basta para confirmar o fato. É o documento ou o comunicado original.

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

## Tier 1 — Jornalismo de apuração

Exigir **2 veículos independentes**, ou **1 + link explícito ao documento primário**
(e nesse caso abra o primário também).

**Internacional**
Reuters, Associated Press, Bloomberg, Financial Times, The Wall Street Journal,
The Economist, The New York Times, The Washington Post, The Information, Axios, Wired,
MIT Technology Review, IEEE Spectrum, Nikkei Asia, SemiAnalysis (análise técnica),
Stat News (IA em saúde).

**Brasil**
Valor Econômico, Folha de S.Paulo, O Estado de S. Paulo / Broadcast, O Globo / g1,
Agência Brasil, Núcleo Jornalismo, Brazil Journal, NeoFeed, Convergência Digital,
Mobile Time, Teletime, JOTA (regulatório e jurídico).

## Tier 2 — Só como corroboração

Nunca fonte única. Servem para confirmar um fato que já tem T0 ou T1.

TechCrunch, The Verge, VentureBeat, Ars Technica, Engadget, ZDNet,
InfoMoney, Exame, Olhar Digital, Tilt/UOL, Canaltech, Tecnoblog.

## Rejeitar sempre

- Agregadores sem atribuição e content farms de notícias de IA. Sinais: sem byline,
  sem redação identificável, texto que parece gerado, dezenas de posts por dia,
  domínio sem página "sobre" com equipe real.
- Posts de LinkedIn, threads no X/Twitter, Reddit, Medium e Substack pessoal **como fonte
  única**. Valem apenas como pista para rastrear a fonte primária — rastreie e cite a primária.
- Resumos em vídeo e transcrições de YouTube.
- Releases de wire promocional (PRNewswire, Business Wire, GlobeNewswire) sem cobertura
  independente. O release em si pode ser T0 se for o comunicado oficial da empresa
  envolvida — a distinção é essa.
- Republicação de agência sem valor adicionado. Prefira sempre o original.
- **Qualquer URL que você não tenha conseguido abrir nesta execução.**

## Nota sobre paywall

Valor, FT, WSJ, Bloomberg e The Information são onde a notícia boa costuma estar, e são
exatamente o que o `WebFetch` não lê. Nesse caso: confirme o fato por fonte aberta,
marque `paywall: true`, cite a fonte paga como corroboração e **não escreva o resumo como
se tivesse lido a matéria**.
