# Formatos — registro, dados, estado e e-mail

O **JSON é a fonte da verdade**. Escreva-o primeiro e derive o Markdown dele.
Nunca escreva o Markdown à mão e depois tente refletir no JSON — é assim que o formato
começa a derivar entre os dias.

---

## 1. Registro diário — `radar-ia/registros/<ano>/<D>.md`

````markdown
---
data_execucao: 2026-09-12
hora_execucao_brt: "08:17"
janela: "2026-09-05 a 2026-09-12"
total_candidatos: 41
total_verificados: 23
descartados_recencia: 11
descartados_verificacao: 7
itens_novos: 6
itens_continuacao: 4
status_execucao: ok            # ok | parcial | falha
entregas:
  commit: ok
  email: ok
  artifact: ok
gerado_por: radar-noticias-ia v1
---

# Radar de IA — 12 de setembro de 2026

## Resumo executivo da semana

- (movimento dominante)
- (regulação)
- (capital e mercado)
- (Brasil — ou a constatação explícita de ausência)
- (o que mudou em relação à semana anterior)

**O que observar nos próximos dias:** parágrafo curto.

---

## Top 10

### 1. Título da notícia em português

| Campo | Valor |
|---|---|
| Link | https://... |
| Fonte | OpenAI — blog oficial |
| Data de publicação | 2026-09-10 |
| Tier | T0 — fonte primária |
| Verificação | CONFIRMADO — link aberto, data de `article:published_time`, corroborado pela Reuters |
| Corroborações | https://... , https://... |
| Status | NOVO |
| Tags | modelos, enterprise |
| Score | 8,7 |

**Resumo.** Duas a três frases factuais.

**Por que importa.** Uma a duas frases orientadas a decisão.

---

### 2. ...

(mesma estrutura para os 10)

---

## Em evolução

Itens já reportados antes que tiveram desdobramento.

| Item | Primeiro registro | O que mudou hoje | Link |
|---|---|---|---|

## Descartados relevantes

O que circulou bastante e não passou na verificação. **Esta seção é o que impede o mesmo
boato de voltar amanhã.**

| Título | Motivo do descarte |
|---|---|

## Notas de execução

- Consultas realizadas: N
- Falhas de WebFetch: URL e motivo (404, paywall, timeout)
- Avisos: ...
````

---

## 2. Dados — `radar-ia/dados/<ano>/<D>.json`

```json
{
  "versao_schema": 1,
  "data_execucao": "2026-09-12",
  "janela": { "inicio": "2026-09-05", "fim": "2026-09-12" },
  "status_execucao": "ok",
  "metricas": {
    "candidatos": 41, "verificados": 23,
    "descartados_recencia": 11, "descartados_verificacao": 7,
    "novos": 6, "continuacao": 4, "webfetch_usados": 34
  },
  "resumo_executivo": { "bullets": ["..."], "observar": "..." },
  "itens": [
    {
      "posicao": 1,
      "id": "2026-09-10-openai-exemplo",
      "chave_dedup": "openai.com/index/exemplo|titulo-em-slug",
      "titulo": "...",
      "link": "https://...",
      "fonte": { "nome": "OpenAI", "natureza": "blog oficial", "pais": "US" },
      "data_publicacao": "2026-09-10",
      "data_confirmada_por": "meta article:published_time",
      "tier": "T0",
      "paywall": false,
      "verificacao": {
        "status": "CONFIRMADO",
        "checks": ["V1", "V2", "V3", "V4"],
        "notas": "..."
      },
      "corroboracoes": ["https://..."],
      "status_temporal": "NOVO",
      "primeiro_registro": "2026-09-12",
      "dias_reportado": 1,
      "tags": ["modelos", "enterprise"],
      "scores": {
        "d1_impacto": 9, "d2_magnitude": 8, "d3_brasil": 4,
        "d4_ineditismo": 9, "d5_credibilidade": 10, "d6_acionabilidade": 7,
        "final": 8.7
      },
      "resumo": "...",
      "por_que_importa": "..."
    }
  ],
  "em_evolucao": [
    { "titulo": "...", "link": "https://...", "primeiro_registro": "2026-09-09", "o_que_mudou": "..." }
  ],
  "descartados": [
    { "titulo": "...", "link": "https://...", "motivo": "sem data confirmável" }
  ],
  "falhas": []
}
```

---

## 3. Estado — `radar-ia/estado.json`

```json
{
  "versao_schema": 1,
  "artifact": {
    "url": null,
    "id": null,
    "atualizado_em": null,
    "falhas_consecutivas": 0
  },
  "ultima_execucao": {
    "data": null,
    "iniciada_em": null,
    "status": null,
    "entregas": { "commit": false, "email": false, "artifact": false },
    "erro": null
  },
  "ultimo_registro": null,
  "email_destino": "marcos.sanchez@lit.com.br",
  "itens_reportados": [],
  "pendencias": []
}
```

`itens_reportados[]`: `{ chave, titulo, primeiro_registro, ultimo_registro, dias }`.
Mantém janela de **14 dias** — o dobro da janela de notícias, para cobrir reaparições.
Pode a cada execução.

`pendencias[]`: `{ data, tipo, detalhe }`. Tipos: `email`, `artifact`, `push`, `busca`.
**Duas pendências consecutivas do mesmo tipo** disparam um alerta no topo do registro
do dia seguinte.

---

## 4. Índice — `radar-ia/indice.md`

Mais recente no topo. Um dia faltando nesta tabela é o sinal mais direto de que uma
execução falhou em silêncio.

```markdown
| Data | Itens | Manchete nº 1 | Status | Registro |
|---|---|---|---|---|
| 2026-09-12 | 10 | Nvidia fecha acordo para comprar a Hugging Face | ok | [ver](registros/2026/2026-09-12.md) |
```

---

## 5. E-mail

**Assunto:** `Radar de IA — <D> — <manchete nº 1 abreviada em ~60 caracteres>`
Prefixe com `[PARCIAL]` ou `[FALHA]` quando `status_execucao` não for `ok`.

**Corpo** (`htmlBody`, com `body` em texto puro como alternativa):

1. Linha de abertura: data, janela coberta, "X novas hoje, Y em evolução".
2. Aviso, se houver: lacuna de execução, pendência repetida, status parcial.
3. **Resumo executivo** — os bullets.
4. **Os 10 itens**, numerados: título como link para a matéria, linha fina com
   veículo · data · tier · status, o resumo e o "por que importa".
5. Rodapé: link para o registro no GitHub e link para a página do Radar.

Regras: links sempre em `<a href>` clicável, nunca Markdown cru. Sem imagem externa
(muitos clientes bloqueiam). Legível em tela de celular — é às 8h da manhã que ele abre.
