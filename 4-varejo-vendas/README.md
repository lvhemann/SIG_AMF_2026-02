# Atividade 4 — Casa & Campo Materiais
## Pedidos do varejo

**Atividade de casa 4 de 6 · limpeza + número escrito como texto**
Disciplina **G0397 — Sistemas de Informação Gerenciais** · AMF Faculdade
Conteúdo de apoio: Aula 04 — indicadores e concentração

---

## A situação

A Casa & Campo tem loja física em Santa Maria e vende também por telefone,
WhatsApp e site. O dono quer duas coisas antes de fechar o orçamento do ano: saber
**quanto vendeu de verdade** e **onde a venda se concentra**, para decidir em que
categoria vai investir estoque.

O sistema de frente de caixa exporta os valores já formatados para leitura humana —
`R$ 1.234,50`. É bonito na tela e inútil para somar.

**Seu papel:** você é a pessoa chamada para transformar esse arquivo em resposta.

---

## Os dados

`dados/pedidos_varejo.csv` — 682 linhas · 12 colunas

| Coluna | O que é | O que se espera |
|---|---|---|
| `pedido_id` | número do pedido | inteiro, único |
| `data` | data do pedido | data |
| `cliente` | nome do cliente | nome e sobrenome |
| `cidade` | cidade do cliente | uma das 5 cidades atendidas |
| `vendedor` | quem atendeu | um dos 5 vendedores |
| `categoria` | categoria do produto | uma das 6 categorias |
| `produto` | item vendido | texto livre |
| `quantidade` | unidades vendidas | inteiro positivo |
| `valor_unitario` | preço unitário | texto no formato R$ 0.000,00 |
| `frete` | frete cobrado | texto no formato R$ 0.000,00 |
| `canal` | por onde veio o pedido | Loja física, Telefone, WhatsApp ou Site |
| `status` | situação do pedido | Faturado ou Cancelado |

> A coluna "o que se espera" descreve o **mundo ideal**. O arquivo real é outra história.

---

## As três perguntas

1. **Qual foi o faturamento do período e o ticket médio?** (declare se o frete entra na conta)

2. **Quantas categorias respondem por 80% do faturamento?** É a curva ABC — e ela decide o estoque.

3. **Como está o desempenho por vendedor e por canal?** Compare com o tamanho de cada grupo.

---

## Por onde começar

1. **`valor_unitario`** — converter o texto em número. É o primeiro passo, e sem ele nada funciona.
2. **`quantidade`** — validar a faixa possível. Há 0, negativo e 999.
3. **`categoria`** — padronizar. Depois do `upper()` sobram 9 grafias para 6 categorias.
4. **`status`** — não precisa padronizar, mas precisa ser usado: pedido cancelado não é faturamento.

**As demais colunas já vêm limpas.** Não perca tempo com elas.

---

## Armadilhas desta base

- **`pd.to_numeric` lê zero dos 660 valores** desta base. O valor está como texto brasileiro: `R$`, ponto de milhar e vírgula decimal. Quem não converter vai somar `NaN` e achar que o faturamento é zero.

- **Pedido cancelado não é faturamento.** Decida — e escreva — se ele entra no ticket médio.

- **Há quantidade zero, negativa e 999.** Uma delas pode ser devolução; outra é erro de digitação. Você decide, mas justifique.

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

URL = "https://raw.githubusercontent.com/SEU-USUARIO/atividades-casa-sig/main/4-varejo-vendas/dados/pedidos_varejo.csv"
pedidos = pd.read_csv(URL)

print(pedidos.shape)
pedidos.head()
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
