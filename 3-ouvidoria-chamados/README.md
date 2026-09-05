# Atividade 3 — Ouvidoria Municipal
## Chamados de cidadãos

**Atividade de casa 3 de 6 · limpeza + datas e duração**
Disciplina **G0397 — Sistemas de Informação Gerenciais** · AMF Faculdade
Conteúdo de apoio: Aula 3.5 — o custo do erro e a regra documentada

---

## A situação

A prefeitura promete, no portal, responder qualquer chamado da ouvidoria em
**até 10 dias**. O secretário de administração quer saber se a promessa se cumpre — e
descobrir onde ela não se cumpre, antes que alguém descubra por ele.

O sistema da ouvidoria exportou os chamados do ano. As datas foram preenchidas à mão em
boa parte dos casos, por atendentes que às vezes registram o fechamento no dia em que
lembram, e não no dia em que aconteceu.

**Seu papel:** você é a pessoa chamada para transformar esse arquivo em resposta.

---

## Os dados

`dados/chamados_ouvidoria.csv` — 500 linhas · 8 colunas

| Coluna | O que é | O que se espera |
|---|---|---|
| `protocolo` | número do protocolo | inteiro, único |
| `bairro` | bairro do chamado | um dos 7 bairros |
| `categoria` | tipo de problema | uma das 6 categorias |
| `canal` | por onde o chamado entrou | 156, Portal, Presencial ou WhatsApp |
| `data_abertura` | quando o cidadão abriu | data |
| `data_fechamento` | quando foi concluído | data, vazio se ainda aberto |
| `status` | situação do chamado | Concluído ou Em aberto |
| `nota_satisfacao` | avaliação do cidadão | de 1 a 5, vazio se não avaliou |

> A coluna "o que se espera" descreve o **mundo ideal**. O arquivo real é outra história.

---

## As três perguntas

1. **Qual o tempo médio de atendimento** dos chamados concluídos?

2. **Que percentual fica dentro do prazo de 10 dias?** E como isso varia por categoria?

3. **Qual combinação de bairro e categoria** merece uma equipe dedicada? Justifique.

---

## Por onde começar

1. **`data_abertura` e `data_fechamento`** — converter com `to_datetime()` e calcular a duração em dias.
2. **duração negativa** — chamado fechado antes de aberto. Decidir o que fazer com essas linhas antes de tirar qualquer média.
3. **`categoria`** — padronizar. Depois do `upper()` sobram 10 grafias para 6 categorias.
4. **`nota_satisfacao`** — validar a escala de 1 a 5, só se você for falar de satisfação.

**As demais colunas já vêm limpas.** Não perca tempo com elas.

---

## Armadilhas desta base

- **Existem chamados fechados antes de terem sido abertos.** Uma duração negativa não é um chamado rápido: é registro errado, e puxa a média para baixo. Com essas linhas o tempo médio dá 7,6 dias; sem elas, 9,2.

- **Chamado em aberto não é chamado sem data.** Um está em andamento; o outro é falha de preenchimento.

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

URL = "https://raw.githubusercontent.com/SEU-USUARIO/atividades-casa-sig/main/3-ouvidoria-chamados/dados/chamados_ouvidoria.csv"
chamados = pd.read_csv(URL)

print(chamados.shape)
chamados.head()
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
