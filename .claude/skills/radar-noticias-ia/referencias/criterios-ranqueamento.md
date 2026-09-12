# Critérios de ranqueamento

Público-alvo: **executivo**. A pergunta que ordena a lista é "isto muda alguma decisão
minha?", não "isto é tecnicamente interessante?".

## Regras duras — aplicadas ANTES de pontuar

Eliminam o item do top 10, sem discussão:

1. Credibilidade (D5) menor que 6.
2. Sem data de publicação confirmada.
3. Status `RUMOR` ou `CONTESTADO`.
4. Link não aberto com sucesso nesta execução.
5. Já reportado antes **sem desdobramento material** → vai para a seção "Em evolução".

## As seis dimensões

Nota de 0 a 10 em cada uma.

| # | Dimensão | Peso | Rubrica |
|---|---|---|---|
| D1 | **Impacto estratégico** | 0,30 | 10 = muda a estratégia de setores inteiros; 7 = muda o planejamento de quem opera no setor; 5 = afeta quem já usa a tecnologia; 2 = interessa a especialistas; 0 = curiosidade |
| D2 | **Magnitude / escala** | 0,15 | 10 = dezenas de bilhões, ou norma nacional/continental; 7 = bilhões, ou milhões de usuários; 5 = centenas de milhões; 2 = dezenas de milhões; 0 = piloto ou anúncio sem número |
| D3 | **Relevância para o Brasil** | 0,15 | 10 = decisão de regulador brasileiro ou investimento no país; 7 = empresa brasileira diretamente envolvida; 5 = global com efeito direto e próximo no Brasil; 2 = efeito indireto; 0 = irrelevante localmente |
| D4 | **Ineditismo** | 0,15 | 10 = primeira vez que se sabe; 7 = confirmação oficial do que era rumor; 5 = desdobramento material; 2 = detalhe novo de algo conhecido; 0 = repetição |
| D5 | **Credibilidade** | 0,15 | 10 = T0 primária; 8 = duas T1 independentes; 6 = uma T1 com link à primária; 4 = uma T1 sozinha; 2 = T2 com corroboração fraca; 0 = sem corroboração |
| D6 | **Acionabilidade** | 0,10 | 10 = exige ação em 90 dias (compliance, contrato, roadmap, orçamento); 7 = deve entrar no planejamento do trimestre; 4 = vale monitorar; 0 = puramente informativo |

**Score final** = `0,30·D1 + 0,15·D2 + 0,15·D3 + 0,15·D4 + 0,15·D5 + 0,10·D6`

Arredonde para uma casa decimal.

## Desempate

Nesta ordem: **D5** (credibilidade) → **D3** (Brasil) → mais recente → melhor tier.

## Regras de diversidade — aplicadas DEPOIS de ordenar

1. **No máximo 3 itens sobre a mesma organização.** O quarto cede lugar ao próximo
   colocado de outra organização. Sem isso, uma semana movimentada de um único lab
   ocupa a lista inteira.
2. **Pelo menos 1 item de regulação ou política**, se houver candidato com score ≥ 6.
3. **Pelo menos 1 item do Brasil**, se houver candidato com score ≥ 5,5.
   Se não houver nenhum elegível, **diga isso explicitamente** no resumo executivo —
   algo como "nenhuma notícia de IA com recorte brasileiro passou na verificação esta
   semana". A ausência é informação; silêncio é omissão.

## Calibragem

Alguns exemplos de referência, para manter as notas estáveis entre os dias:

| Tipo de notícia | D1 típico |
|---|---|
| Regulador de grande mercado publica norma com prazo de adequação | 9-10 |
| Lab de ponta lança modelo com salto de capacidade mensurável | 8-9 |
| Aquisição bilionária que reconfigura a cadeia (chips, dados, distribuição) | 8-9 |
| Rodada de investimento grande sem mudança de posição competitiva | 5-6 |
| Nova funcionalidade de produto já existente | 3-5 |
| Benchmark novo, paper sem aplicação próxima | 2-4 |
| Declaração de executivo sem fato associado | 0-2 |
