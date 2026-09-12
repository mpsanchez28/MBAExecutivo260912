# MBAExecutivo260912 — contrato do repositório

Este repositório hospeda o **Radar de IA**: um agente que roda todo dia às 8h (BRT),
pesquisa notícias de Inteligência Artificial, verifica as fontes e grava um registro
com as 10 mais importantes da última semana.

Se você é uma sessão nova aberta neste repositório, leia isto antes de agir.

## Regras gerais

- **Idioma:** tudo em português brasileiro — registros, e-mails, commits, comentários.
- **Fuso de referência:** `America/Sao_Paulo` (UTC−3 o ano todo; o Brasil não tem mais
  horário de verão). O container roda em UTC — **sempre** obtenha a data com
  `TZ=America/Sao_Paulo date +%F`, nunca da memória do modelo.
- **Branch:** a Routine diária commita em `main`. Trabalho de desenvolvimento vai em
  branch própria.
- **Commit + push sempre:** o hook `Stop` do ambiente bloqueia o fim do turno se houver
  alteração não commitada ou arquivo não rastreado. Não deixe temporários soltos no
  repositório — use o diretório de scratchpad da sessão.

## Regras invioláveis do Radar

> **Nenhum link vai para o registro, o e-mail ou o Artifact sem ter sido aberto com
> sucesso por `WebFetch` nesta execução.**

- Nunca invente ou estime data de publicação. Sem data confirmada, o item não entra no top 10.
- O commit do registro **nunca** depende do sucesso do e-mail nem do Artifact.
  Git é a fonte da verdade; as outras entregas são best-effort.
- O JSON é a fonte da verdade; o Markdown é derivado dele. Nunca o contrário.
- Se a pesquisa render menos de 5 itens verificados, gere o registro assim mesmo com
  `status_execucao: parcial` e avise no e-mail. Nunca produza silêncio.

## Mapa do repositório

| Caminho | O que é |
|---|---|
| `.claude/skills/radar-noticias-ia/SKILL.md` | O procedimento do agente (o "cérebro") |
| `.claude/skills/radar-noticias-ia/referencias/` | Fontes, protocolo de verificação, critérios, formatos |
| `radar-ia/estado.json` | Estado entre execuções: URL do Artifact, última execução, itens já reportados |
| `radar-ia/indice.md` | Uma linha por dia — um dia faltando indica falha silenciosa |
| `radar-ia/registros/<ano>/<AAAA-MM-DD>.md` | Registro diário legível |
| `radar-ia/dados/<ano>/<AAAA-MM-DD>.json` | Mesmo conteúdo, estruturado |
| `radar-ia/artefato/radar-ia.html` | Cópia do último HTML publicado (fallback e diff) |

## Como rodar manualmente

Peça "roda o radar de IA" ou invoque a skill `radar-noticias-ia`.
Para testar sem efeitos externos, diga explicitamente: **"sem enviar e-mail e sem publicar o Artifact"**.
