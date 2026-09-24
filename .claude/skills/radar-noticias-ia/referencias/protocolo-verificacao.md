# Protocolo de verificação de fontes

> **Nenhum link vai para o registro, o e-mail ou o Artifact sem ter sido aberto com
> sucesso por `WebFetch` nesta execução.**

## Os cinco checks

### V1 — Link vivo e correto

`WebFetch` na URL. Confirme:

- resposta útil (não 404, não página de erro);
- o **título da página corresponde** ao candidato;
- o **corpo trata do assunto** que você espera.

Detecte e trate:

| Situação | Ação |
|---|---|
| Redirecionamento para a home do veículo | A matéria saiu do ar ou a URL estava errada → descartar |
| 404 / erro | Descartar |
| Assunto divergente do título do candidato | Descartar — sinal clássico de URL montada por inferência |
| Paywall duro (menos de ~300 caracteres úteis) | Buscar o mesmo fato em fonte aberta. Sem alternativa: `paywall: true` e **fora do top 10** |

**Domínios bloqueados.** Alguns veículos recusam o nosso user agent e a ferramenta
devolve erro antes de qualquer tentativa (`reuters.com`, `apnews.com`, `ft.com`, `wsj.com`,
`theverge.com`, `arstechnica.com`, `wired.com` — lista completa e atualizada em
`fontes-confiaveis.md`). Isso é falha de V1 como qualquer outra: o item não entra no top 10
por essa fonte. Procure o fato numa fonte primária ou num veículo acessível. Ao encontrar um
domínio recusado que não está catalogado, **acrescente-o a `fontes-confiaveis.md` no commit
do dia**.

### V2 — Data de publicação

Extraia a data de, em ordem de preferência:

1. meta `article:published_time`
2. JSON-LD `datePublished`
3. `<time datetime="...">`
4. dateline visível no corpo

Grave a origem em `data_confirmada_por` (ex.: `"meta article:published_time"`).

**Armadilha:** muitos veículos mostram a data de **atualização**, não a de publicação.
Uma matéria de 2024 reeditada ontem não é notícia da semana. Se as duas datas existirem,
use a de publicação e anote a divergência.

Sem data: procure a mesma notícia em segunda fonte que tenha data. Se continuar ambígua,
vai para "Descartados relevantes" com o motivo `sem data confirmável`.

**arXiv:** vale a data de submissão da versão relevante. Se a notícia é sobre a v2,
use a data da v2, não a da v1.

### V3 — Fonte primária ou N independentes

| Tier da melhor fonte | Exigência |
|---|---|
| **T0** | Basta ela |
| **T1** | Dois veículos independentes, **ou** um T1 que linke o documento primário — e então abra o primário também |
| **T2** | Nunca sozinho. Precisa de T0 ou T1 junto |

"Independentes" exclui dois veículos republicando o mesmo despacho de agência: Reuters e
um jornal que reproduz a Reuters contam como **uma** fonte.

Não atingiu o mínimo → status `PARCIAL`. Só entra no top 10 com credibilidade ≥ 6.

### V4 — Contradição e correção

Obrigatório para todo item que envolva **cifra, aquisição, demissão em massa, processo
judicial ou anúncio de produto**: uma busca adicional procurando desmentido —
`denies`, `retracts`, `walks back`, `corrigido`, `desmente`.

Achou → status `CONTESTADO`, fora do top 10, registrado nos descartados com o link do
desmentido.

### V5 — Classificação final

| Status | Critério | Pode entrar no top 10? |
|---|---|---|
| `CONFIRMADO` | V1 + V2 + V3 ok, V4 limpo | Sim |
| `PARCIAL` | V1 + V2 ok, V3 incompleto | Só com credibilidade ≥ 6 |
| `RUMOR` | Só fonte secundária, sem primária, sem corroboração | Não. Cabe uma seção "Radar de rumores", sempre rotulada |
| `CONTESTADO` | Desmentido ou corrigido | Não |

## Filtro de recência

- Janela `[D-7, D]` inclusive, com `D` obtido de `TZ=America/Sao_Paulo date +%F`.
- Converta a data da fonte (que vem no fuso dela) para o **dia civil brasileiro**.
- Publicado em `D-7` entra; em `D-8` não.
- Notícia antiga com **desdobramento dentro da janela** entra pelo desdobramento, com a
  data do desdobramento e `status_temporal: EM EVOLUÇÃO`.
- Sem data confirmada → nunca entra no top 10.

## Orçamento

Máximo de **40 `WebFetch` por execução**. Triagem por score preliminar (título, veículo,
tier), verificação profunda só nos ~15 melhores, V1+V2 nos demais que sirvam de
corroboração. Se bater o teto, pare e registre em `falhas` que o orçamento se esgotou —
não é motivo para abortar a execução.
