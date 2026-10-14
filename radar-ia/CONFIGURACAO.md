# Configuração do agendamento

O agente está pronto no repositório. Faltam **dois pré-requisitos de ambiente** que só
você pode liberar, e então a criação da Routine.

---

## Pré-requisito 1 — acesso de rede (bloqueante)

O agente precisa **abrir cada link** para verificar fonte e data. Sem isso ele não produz
nada: pela regra V1, link que não abre não é publicado.

Medido em 14/10/2026 no ambiente `MBA_Aula`, de 14 domínios testados **apenas
`anthropic.com` respondeu**. Inacessíveis: `openai.com`, `blog.google`, `deepmind.google`,
`ai.meta.com`, `nvidianews.nvidia.com`, `www.cnbc.com`, `techcrunch.com`, `www.axios.com`,
`arxiv.org`, `digital-strategy.ec.europa.eu`, `convergenciadigital.com.br`,
`agenciabrasil.ebc.com.br`, `www.jota.info`.

**O que fazer.** No menu do ambiente, na barra de título da sessão → **Edit** →
**Network access**: escolha um nível mais amplo, ou adicione os domínios do
`fontes-confiaveis.md` à lista de permitidos. Os níveis estão descritos em
https://code.claude.com/docs/en/claude-code-on-the-web

**Atalho que provavelmente resolve sozinho:** ao criar a Routine (abaixo), aponte-a para um
ambiente com acesso de rede amplo — o `Default — trusted network access`, por exemplo — em
vez do `MBA_Aula`. O agendamento não precisa rodar no mesmo ambiente desta sessão.

Para conferir se já está liberado, peça numa sessão: *"testa se o radar alcança as fontes"*.

## Pré-requisito 2 — conector do Gmail

O e-mail diário depende do conector do Gmail estar anexado à Routine. **Isso não pode ser
feito por API nesta organização** (o parâmetro `connectors` é rejeitado, e uma Routine
criada por API dispara sessão sem conector nenhum e sem o repositório clonado). É por isso
que a Routine precisa ser criada pela interface.

---

## Criar a Routine

Em **claude.ai → Routines → nova Routine**, com:

| Campo | Valor |
|---|---|
| Nome | `Radar de IA — boletim diário 08h BRT` |
| Agendamento | todo dia, **08:17** (horário de Brasília) |
| Ambiente | um com **acesso de rede amplo** (ver pré-requisito 1) |
| Repositório | `mpsanchez28/MBAExecutivo260912`, branch `main` |
| Conectores | **Gmail** |
| Notificações | push ativado |

O minuto 17 em vez de 08:00 é deliberado: evita a fila de agendamentos que todo mundo
coloca na hora cheia.

### Prompt da Routine

Cole exatamente isto no campo de prompt:

```text
Você é o agente do Radar de IA do repositório MBAExecutivo260912. Esta é uma execução
automática diária, sem ninguém acompanhando. Trabalhe de forma autônoma e não faça
perguntas.

CONTEXTO
- Branch de trabalho: main.
- Idioma de todas as entregas: português brasileiro.
- Fuso de referência: America/Sao_Paulo (UTC-3 o ano todo).
- Destinatário do e-mail: marcos.sanchez@lit.com.br

PASSO 1 - Preparar
  Na raiz do repositório: git fetch origin && git checkout main && git pull --rebase origin main
  Determine a data de hoje com: TZ=America/Sao_Paulo date +%F   (chame-a de D)

PASSO 2 - Pesquisar
  Invoque a skill radar-noticias-ia e siga o procedimento dela integralmente, do início ao
  fim, sem pular fases. Se a skill não for encontrada, leia
  .claude/skills/radar-noticias-ia/SKILL.md e execute o procedimento manualmente.

PASSO 3 - Commitar e enviar (obrigatório, antes das entregas externas)
  Confira que existem radar-ia/dados/<ano>/<D>.json, radar-ia/registros/<ano>/<D>.md,
  radar-ia/estado.json atualizado e radar-ia/indice.md atualizado.
  git add -A && git commit -m "radar-ia: registro de <D>" && git push origin main
  Se o push divergir: git pull --rebase origin main, resolva (em estado.json preserve a
  UNIÃO de itens_reportados) e repita. No máximo 3 tentativas.

PASSO 4 - E-mail
  Envie com mcp__Gmail__send_message para marcos.sanchez@lit.com.br seguindo o template de
  .claude/skills/radar-noticias-ia/referencias/formatos.md (use htmlBody para os links
  ficarem clicáveis).
  Assunto: "Radar de IA - <D> - <manchete nº1 abreviada>", prefixado com "[PARCIAL]" ou
  "[FALHA]" se o status da execução não for ok.
  Se o e-mail falhar, NÃO reverta nada: grave entregas.email = false e a pendência em
  estado.json, e siga em frente.

PASSO 5 - Página publicada
  Leia radar-ia/estado.json -> artifact.url.
  - Com URL: abra-a com a ferramenta Artifact (action "read") e republique na MESMA URL.
  - Sem URL, ou após 2 falhas consecutivas de leitura: publique uma nova e grave url e id
    em estado.json, zerando falhas_consecutivas.
  - Se a ferramenta Artifact não existir nesta sessão: grave só o HTML, anote a pendência,
    mencione no e-mail e siga.
  Sempre salve a cópia em radar-ia/artefato/radar-ia.html.

PASSO 6 - Fechamento
  Se o PASSO 5 alterou arquivos, faça um segundo commit e push. NÃO termine com nada não
  commitado nem arquivo não rastreado - o hook de fim de sessão bloqueia isso.
  Encerre com um resumo de 5 linhas: quantos itens, status de cada entrega, pendências.

REGRAS INVIOLÁVEIS
- Nunca publique um link que você não tenha aberto com sucesso nesta execução.
- Nunca invente data de publicação. Sem data confirmada, o item não entra no top 10.
- O commit do registro nunca depende do sucesso do e-mail nem da página.
- Se a rede bloquear as fontes (erro de egresso ou DNS), NÃO monte o boletim só com
  resultados de busca. Grave status_execucao "falha" com o motivo, commite, e envie o
  e-mail com prefixo [FALHA] explicando que o acesso de rede do ambiente precisa ser
  ampliado.
- Se a pesquisa render menos de 5 itens verificados, gere o registro assim mesmo com
  status_execucao "parcial" e envie o e-mail com prefixo [PARCIAL]. Nunca produza silêncio.
```

---

## Depois de criar

1. Rode a Routine manualmente uma vez (**Run now**) e confira os quatro pontos: commit
   novo no GitHub, e-mail recebido, página publicada, e `radar-ia/estado.json` com as três
   entregas em `true`.
2. Guarde a URL da página que a primeira execução publicar — ela fica gravada em
   `estado.json` e é reutilizada todo dia.
3. No dia seguinte, confira se `radar-ia/indice.md` ganhou a linha do dia. **Um dia faltando
   nesse índice é o sinal de que uma execução falhou em silêncio.**
