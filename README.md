# MBAExecutivo260912 — Radar de IA

Agente que roda **todo dia às 8h (BRT)**, pesquisa notícias de Inteligência Artificial na
internet, **verifica as fontes uma a uma**, filtra os últimos 7 dias e registra as **10
mais importantes da semana**, com links, resumo e "por que importa".

Três entregas por execução:

1. **Registro no repositório** — `radar-ia/registros/<ano>/<data>.md`, commitado em `main`.
2. **E-mail diário** — resumo com os 10 itens e links clicáveis.
3. **Página do Radar** — Artifact publicado, atualizado na mesma URL todo dia.

## Como funciona

```
Routine (cron 11:17 UTC = 08:17 BRT)
   └─ sessão nova, sem contexto anterior
        ├─ skill radar-noticias-ia
        │     coleta → verificação → recência → dedup → ranking → redação → gravação
        ├─ commit + push        ← primeiro, sempre
        ├─ e-mail               ← best-effort
        └─ Artifact             ← best-effort
```

O commit vem **antes** do e-mail e da página. O git é a fonte da verdade; se uma das
outras entregas falhar, o registro do dia existe do mesmo jeito e a falha fica anotada
em `radar-ia/estado.json`.

### Verificação de fontes

É o núcleo do agente, não um detalhe. Uma busca por "AI news this week" devolve, logo nos
primeiros resultados, agregadores e conteúdo gerado sem atribuição. Por isso cada
candidato passa por cinco checagens — link vivo e correto, data de publicação extraída dos
metadados, fonte primária ou duas fontes independentes, busca por desmentido, e
classificação final — descritas em
[`protocolo-verificacao.md`](.claude/skills/radar-noticias-ia/referencias/protocolo-verificacao.md).

O catálogo de fontes é **curado e versionado** em
[`fontes-confiaveis.md`](.claude/skills/radar-noticias-ia/referencias/fontes-confiaveis.md),
em três tiers, com lista explícita do que rejeitar.

> **Regra inviolável:** nenhum link é publicado sem ter sido aberto com sucesso na própria
> execução. URL alucinada é o pior modo de falha deste agente, porque o erro só aparece
> quando alguém clica.

### Ranqueamento

Seis dimensões com pesos, calibradas para público executivo — impacto estratégico (0,30),
magnitude (0,15), relevância para o Brasil (0,15), ineditismo (0,15), credibilidade (0,15)
e acionabilidade (0,10) — mais regras de diversidade que impedem um único lab de ocupar a
lista inteira. Detalhes em
[`criterios-ranqueamento.md`](.claude/skills/radar-noticias-ia/referencias/criterios-ranqueamento.md).

Como o ranking é **semanal e móvel** e a execução é diária, há sobreposição entre dias
consecutivos por desenho. Os selos `NOVO` e `EM EVOLUÇÃO` e a seção "Em evolução" mostram
o que mudou desde ontem sem perder o panorama da semana.

## Estrutura

| Caminho | O que é |
|---|---|
| `.claude/skills/radar-noticias-ia/SKILL.md` | O procedimento do agente |
| `.claude/skills/radar-noticias-ia/referencias/` | Fontes, protocolo, critérios, formatos, template da página |
| `radar-ia/estado.json` | URL do Artifact, última execução, itens já reportados, pendências |
| `radar-ia/indice.md` | Uma linha por dia — lacuna indica falha silenciosa |
| `radar-ia/registros/<ano>/` | Registros diários em Markdown |
| `radar-ia/dados/<ano>/` | Mesmo conteúdo em JSON (fonte da verdade) |
| `radar-ia/artefato/radar-ia.html` | Cópia do último HTML publicado |

## Uso manual

- **Rodar agora:** peça "roda o radar de IA" numa sessão do Claude Code neste repositório.
- **Testar sem efeitos externos:** "roda o radar **sem enviar e-mail e sem publicar o Artifact**".
- **Pausar o agendamento:** peça para desabilitar a Routine "Radar de IA — boletim diário 08h BRT".
- **Mudar o horário, o destinatário ou as fontes:** horário e destinatário ficam na Routine e em
  `radar-ia/estado.json`; as fontes, em `fontes-confiaveis.md` — acrescente com um commit,
  nunca no meio de uma execução.
