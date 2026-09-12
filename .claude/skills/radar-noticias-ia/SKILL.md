---
name: radar-noticias-ia
description: >-
  Use esta skill SEMPRE que o usuário pedir o radar/boletim das principais notícias de
  Inteligência Artificial da última semana. Dispare com frases como "roda o radar de IA",
  "monta o boletim de IA de hoje", "quais as 10 notícias mais importantes de IA da semana",
  "atualiza o radar de notícias", "/radar-noticias-ia", e também quando uma Routine agendada
  mandar executar o radar diário. A skill PESQUISA na web notícias globais (inglês) e do
  Brasil (português), VERIFICA cada fonte abrindo o link e classificando sua confiabilidade
  em tiers, FILTRA apenas os últimos 7 dias, DEDUPLICA contra os registros dos dias
  anteriores, RANQUEIA as 10 mais relevantes para um público executivo e GRAVA um registro
  em Markdown mais um JSON no repositório, com links, resumo e "por que importa". NÃO use
  para fechamento de mercado financeiro (use fechamento-b3 / fechamento-dow-jones) nem para
  resumir um artigo científico específico (use resume-artigo).
---

# Radar de Notícias de IA

Produz o boletim diário das 10 notícias de IA mais importantes dos últimos 7 dias,
com fontes verificadas, e grava o registro no repositório.

> **REGRA INVIOLÁVEL — leia antes de qualquer coisa**
>
> **Nenhum link vai para o registro, o e-mail ou o Artifact sem ter sido aberto com
> sucesso por `WebFetch` NESTA execução.** Não existe exceção. URL alucinada é o modo
> de falha mais grave deste agente, porque o erro só aparece quando o usuário clica.

## Arquivos de referência

Carregue sob demanda, na fase indicada — não leia todos de uma vez:

| Arquivo | Quando ler |
|---|---|
| `referencias/fontes-confiaveis.md` | Fases 1 e 2 |
| `referencias/protocolo-verificacao.md` | Fase 2 e 3 |
| `referencias/criterios-ranqueamento.md` | Fase 5 |
| `referencias/formatos.md` | Fases 6, 7 e 8 (templates do MD, do JSON e do e-mail) |
| `referencias/template-artefato.html` | Fase 8, só se for publicar o Artifact |

---

## Fase 0 — Pré-condições

1. **Data.** `D = $(TZ=America/Sao_Paulo date +%F)`. Nunca use a data que você "acha"
   que é hoje — no container o relógio está em UTC e depois das 21h BRT a data civil
   brasileira já difere.
2. **Janela:** `[D-7, D]`, inclusive nas duas pontas.
3. **Caminhos:** `radar-ia/dados/<ano>/<D>.json` e `radar-ia/registros/<ano>/<D>.md`.
   Crie os diretórios do ano se não existirem.
4. **Estado.** Leia `radar-ia/estado.json`. Se não existir, crie com a estrutura de
   `referencias/formatos.md`.
5. **Idempotência.** Se `<D>.md` já existe e `estado.json.ultima_execucao.data == D`,
   entre em **modo reexecução**: refaça a pesquisa e sobrescreva o registro, mas
   **não reenvie o e-mail** se `entregas.email == true`.
6. **Lacuna.** Se `estado.json.ultima_execucao.data` não for `D-1` (e não for a primeira
   execução), houve falha em algum dia. Registre em `falhas` e abra o e-mail do dia com
   um aviso: "a execução de DD/MM não ocorreu".

## Fase 1 — Coleta

Mínimo de **12 consultas `WebSearch`**, pelo menos 3 em cada bloco. Combine sempre com
recorte temporal (`this week`, `past 7 days`, `últimos 7 dias`):

- **Bloco A — labs e produto (inglês):** OpenAI, Anthropic, Google DeepMind / Gemini,
  Meta / Llama, Mistral, xAI — lançamentos, modelos, mudanças de produto.
- **Bloco B — regulação e jurídico (inglês + português):** EU AI Act, ordens executivas
  e projetos de lei, processos de copyright, controles de exportação de chips.
- **Bloco C — capital e mercado (inglês):** rodadas bilionárias, aquisições, investimento
  em datacenter, resultados de Nvidia e afins.
- **Bloco D — Brasil (português):** PL 2338/2023, ANPD, MCTI, CADE, investimento e adoção
  de IA por empresas brasileiras, datacenters no país.

**Alvo:** 30 a 50 candidatos, cada um com título, URL, veículo e data alegada.
Exija **pelo menos 2 candidatos de origem Brasil** no conjunto bruto — se o bloco D não
render, refaça-o com outros termos antes de seguir.

Consulte `referencias/fontes-confiaveis.md` e descarte de saída o que estiver na lista de
rejeição. Não gaste `WebFetch` em content farm.

## Fase 2 — Verificação

Leia `referencias/protocolo-verificacao.md` e aplique os checks **V1 a V5**.

**Orçamento:** no máximo **40 `WebFetch` por execução**. Para respeitá-lo:
1. Dê um score preliminar a todos os candidatos usando só título, veículo e tier.
2. Verifique a fundo (V1-V5) os ~15 melhores.
3. Verifique só V1+V2 no restante que possa entrar como corroboração.

Classifique cada item como `CONFIRMADO`, `PARCIAL`, `RUMOR` ou `CONTESTADO`.

## Fase 3 — Recência

Janela `[D-7, D]`, com a data de publicação convertida para o dia civil brasileiro.

- `D-7` entra; `D-8` não.
- Notícia mais antiga com **desdobramento novo dentro da janela** entra pelo desdobramento,
  com a data do desdobramento e status `EM EVOLUÇÃO`.
- Sem data confirmável, o item **não entra no top 10** (ver V2). Vai para
  "Descartados relevantes" com o motivo.

## Fase 4 — Deduplicação

Para cada item calcule `chave_dedup` = URL normalizada (sem `utm_*`, `?ref=`, fragmento,
barra final) + `|` + slug do título.

Compare com `estado.json.itens_reportados` (janela de **14 dias**):

- **Nunca visto** → `status_temporal: NOVO`, `dias_reportado: 1`.
- **Visto, com desdobramento material** (fato novo, número novo, decisão nova) →
  `EM EVOLUÇÃO`, pode disputar o top 10, e o campo `resumo` deve dizer **o que mudou**.
- **Visto, sem desdobramento** → fora do top 10; vai para a seção "Em evolução".

Ao final, pode `itens_reportados`: remova o que tem `ultimo_registro` anterior a `D-14`.

## Fase 5 — Ranqueamento

Leia `referencias/criterios-ranqueamento.md`. Aplique, nesta ordem:

1. **Regras duras** (eliminam antes de pontuar).
2. **Score** ponderado nas 6 dimensões.
3. **Desempate.**
4. **Regras de diversidade** — inclusive: se não houver item do Brasil elegível, diga isso
   **explicitamente** no resumo executivo. A ausência também é informação.

Resultado: exatamente **10 itens**, ordenados. Se houver menos de 10 elegíveis, entregue
os que houver e marque `status_execucao: parcial`.

## Fase 6 — Redação

Português brasileiro, registro executivo. Frases curtas, voz ativa, números concretos.
Sem hype, sem adjetivo vazio, sem emoji, sem "revolucionário" / "game changer".

Por item:
- **Resumo:** 2 a 3 frases, só fato. Quem fez o quê, quando, com que número.
- **Por que importa:** 1 a 2 frases, orientadas a decisão — o que muda para quem dirige
  uma empresa. Se a resposta honesta é "nada ainda", diga isso.

Mais o **resumo executivo da semana**: 4 a 6 bullets (movimento dominante, regulação,
capital, Brasil, o que mudou em relação à semana passada) e um parágrafo curto de
"o que observar nos próximos dias".

## Fase 7 — Gravação

**Nesta ordem** (o JSON é a fonte da verdade; o MD é renderização):

1. Escreva `radar-ia/dados/<ano>/<D>.json` conforme o schema em `referencias/formatos.md`.
2. Derive `radar-ia/registros/<ano>/<D>.md` a partir dele, usando o template.
3. Atualize `radar-ia/estado.json`: `ultima_execucao`, `ultimo_registro`,
   `itens_reportados` (upsert + poda), `pendencias`.
4. Acrescente a linha do dia em `radar-ia/indice.md` (mais recente no topo).

## Fase 8 — Entregas

**A ordem importa: o commit vem primeiro.** O registro no git é a fonte da verdade;
e-mail e Artifact são best-effort e nunca podem abortar a execução nem reverter o commit.

1. **Commit + push.** Confira que os 4 arquivos existem e estão coerentes.
   `git add -A`, commit com mensagem `radar-ia: registro de <D>`, `git push`.
   Se o push divergir: `git pull --rebase`, resolva e repita — até 3 tentativas.
   Em conflito no `estado.json`, preserve a **união** de `itens_reportados`.
2. **E-mail** via `mcp__Gmail__send_message`, template em `referencias/formatos.md`.
   Use `htmlBody` para os links ficarem clicáveis, e preencha `body` com a versão texto.
   Falhou? Grave `entregas.email = false` e a pendência; **siga em frente**.
3. **Artifact.** Leia `estado.json.artifact.url`.
   - Com URL: `Artifact action:"read"` nela e republique na **mesma** URL.
   - Sem URL, ou após **2 falhas consecutivas** de leitura: publique um novo e grave
     URL e id no estado, zerando `falhas_consecutivas`.
   - Ferramenta indisponível na sessão: grave só o HTML, anote a pendência, mencione no
     e-mail e siga.
   - Sempre salve a cópia em `radar-ia/artefato/radar-ia.html`.
4. Se o passo 3 alterou arquivos, **faça um segundo commit e push**. Não termine com nada
   não commitado — o hook `Stop` bloqueia o fim do turno.

## Modos degradados

| Situação | O que fazer |
|---|---|
| Menos de 5 itens verificados | Gera o registro com `status_execucao: parcial`, e-mail com prefixo `[PARCIAL]` explicando o que faltou |
| `WebSearch` falhando repetidamente | Tente termos alternativos; após 3 falhas seguidas, `status_execucao: falha`, registre e envie e-mail `[FALHA]` |
| Sem nenhum item do Brasil elegível | Entrega normal, com a ausência dita explicitamente no resumo executivo |
| Gmail indisponível ou sem o conector | Commit normal, `pendencias` registra; após 2 dias seguidos, alerta no topo do registro |
| `Artifact` indisponível | Commit normal, HTML salvo no repo, pendência registrada |
| Push conflita 3 vezes | Não force. Registre a pendência, deixe o trabalho commitado localmente e relate no e-mail |
