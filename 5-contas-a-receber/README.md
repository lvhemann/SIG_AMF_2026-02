# Atividade 5 — Distribuidora Sul
## Contas a receber

**Atividade de casa 5 de 6 · limpeza + coerência entre colunas**
Disciplina **G0397 — Sistemas de Informação Gerenciais** · AMF Faculdade
Conteúdo de apoio: Aula 04 — medidas e recorte

---

## A situação

A Distribuidora Sul vende para mercados e padarias da região e financia
quase tudo em 30 dias. O caixa está apertado e o financeiro precisa responder ao dono:
**quanto tempo o dinheiro demora para entrar** e **qual forma de pagamento atrasa mais**.

O sistema tem uma coluna `situacao` que diz se o título está pago. O problema é que essa
coluna é atualizada à mão, e as datas são atualizadas por integração bancária. Quando as
duas discordam, alguém tem de decidir em qual acreditar.

**Seu papel:** você é a pessoa chamada para transformar esse arquivo em resposta.

---

## Os dados

`dados/titulos_receber.csv` — 576 linhas · 8 colunas

| Coluna | O que é | O que se espera |
|---|---|---|
| `titulo_id` | número do título | inteiro, único |
| `cliente` | empresa devedora | nome fantasia |
| `emissao` | data de emissão | data |
| `vencimento` | data de vencimento | data |
| `pagamento` | data do pagamento | data, vazio se não pagou |
| `valor` | valor do título | texto no formato R$ 0.000,00 |
| `forma_pagamento` | forma combinada | Boleto, PIX, Cartão, Cheque ou Transferência |
| `situacao` | situação do título | Pago ou Em aberto |

> A coluna "o que se espera" descreve o **mundo ideal**. O arquivo real é outra história.

---

## As três perguntas

1. **Qual o prazo médio de recebimento** (da emissão até o pagamento)?

2. **Que percentual dos títulos é pago em atraso, por forma de pagamento?** Compare com o volume de cada forma.

3. **Como está a carteira em aberto?** Monte o aging: até 30 dias, 31 a 60, mais de 60.

---

## Por onde começar

1. **`emissao`, `vencimento` e `pagamento`** — converter com `to_datetime()`.
2. **coerência entre `situacao` e `pagamento`** — escolher em qual das duas acreditar, e escrever por quê.
3. **`valor`** — converter o texto em número.
4. **`forma_pagamento`** — padronizar. Depois do `upper()` sobram 8 grafias para 5 formas.

**As demais colunas já vêm limpas.** Não perca tempo com elas.

---

## Armadilhas desta base

- **A coluna `situacao` não é confiável.** Há títulos marcados como *Pago* sem data de pagamento, e títulos *Em aberto* que têm data de pagamento. Escolha uma fonte de verdade — e escreva qual, e por quê. A inadimplência muda de 9,5% para 20,6%.

- **Há pagamento anterior à emissão.** Isso não existe no mundo real: é erro de digitação, e entra na conta do prazo médio puxando tudo para baixo.

- **Atraso negativo é pagamento adiantado**, não erro. Some separado: adiantamento e atraso são informações diferentes para quem cuida do caixa.

- **Nunca sobrescreva o arquivo original.** Salve a base tratada com outro nome.

---

## O que entregar

Um notebook do Colab (link compartilhado) com, nesta ordem:

1. **Diagnóstico** — tamanho da base, ausentes por coluna e quantas categorias
   diferentes existem onde deveria haver poucas.
2. **Tratamento** — o pipeline comentado, cada passo dizendo *o que* você fez e *por quê*.
3. **As três respostas**, calculadas sobre a base tratada.
4. **Uma regra documentada** — escolha a decisão de tratamento mais discutível que você
   tomou e escreva: nome, definição em português, fórmula, dono e desde quando vale.
5. **Leitura gerencial** — cinco linhas dizendo o que a direção deveria fazer.

Trabalho em duplas. Prazo e canal conforme combinado com a turma.

---

## Como carregar no Colab

```python
import pandas as pd

URL = "https://raw.githubusercontent.com/SEU-USUARIO/atividades-casa-sig/main/5-contas-a-receber/dados/titulos_receber.csv"
titulos = pd.read_csv(URL)

print(titulos.shape)
titulos.head()
```

Se preferir, baixe o arquivo e use **Arquivos → upload** no Colab.

---

## Checklist antes de entregar

- [ ] Tipos corretos? (data como data, número como número)
- [ ] Ausentes tratados por uma regra escrita, e não caso a caso?
- [ ] Duplicatas resolvidas — e você decidiu qual cópia manter?
- [ ] Categorias padronizadas, e `isna().sum()` igual a zero depois do `map()`?
- [ ] O recorte aparece junto de cada número? (sobre quantas linhas ele foi calculado)
- [ ] A leitura gerencial responde "e daí?" para quem não programa?

---

*Base de dados fictícia, gerada para uso didático na disciplina. Nomes de pessoas, empresas e valores não correspondem a ninguém real.*
