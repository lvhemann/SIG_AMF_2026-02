# Atividade 2 — Metalúrgica Vale Verde
## Ponto e desligamentos

**Atividade de casa 2 de 6 · limpeza + faixas inválidas**
Disciplina **G0397 — Sistemas de Informação Gerenciais** · AMF Faculdade
Conteúdo de apoio: Aula 03 — regra de negócio e `between()`

---

## A situação

O RH da Vale Verde vai apresentar ao conselho, no mês que vem, o custo do
absenteísmo. A frase que o diretor usou foi: *"quero saber quanto a gente paga por gente
que não vem"*.

O sistema de ponto exportou o ano fechado, com os desligamentos do período. A base
mistura registros digitados no chão de fábrica com registros lançados pelo escritório —
e ninguém nunca validou o que estava sendo digitado.

**Seu papel:** você é a pessoa chamada para transformar esse arquivo em resposta.

---

## Os dados

`dados/ponto_rh.csv` — 449 linhas · 9 colunas

| Coluna | O que é | O que se espera |
|---|---|---|
| `registro_id` | número do registro | inteiro, único |
| `colaborador` | nome do colaborador | nome e sobrenome |
| `setor` | setor de lotação | um dos 6 setores |
| `cargo` | cargo ocupado | um dos 5 cargos |
| `admissao` | data de admissão | data |
| `desligamento` | data de desligamento | data, vazio se ainda trabalha |
| `motivo_saida` | motivo do desligamento | vazio se ainda trabalha |
| `faltas_ano` | faltas não justificadas no ano | número de dias |
| `dias_trabalhados` | dias trabalhados no ano | número de dias |

> A coluna "o que se espera" descreve o **mundo ideal**. O arquivo real é outra história.

---

## As três perguntas

1. **Qual é a média de faltas por colaborador?** E por setor?

2. **Qual foi o turnover do período** e qual o motivo de saída mais comum, depois de padronizar?

3. **Qual setor merece atenção primeiro?** Justifique com número e com o tamanho do setor.

---

## Por onde começar

1. **`setor`** — padronizar. Depois do `upper()` sobram 10 grafias para 6 setores.
2. **`faltas_ano`** — decidir qual faixa de faltas é possível em um ano e marcar o resto como ausente com `.loc`. Há gente com 999 e com faltas negativas.
3. **`motivo_saida`** — padronizar, mas só se você for responder a pergunta 2. São 6 grafias para 4 motivos.

**As demais colunas já vêm limpas.** Não perca tempo com elas.

---

## Armadilhas desta base

- **Existe gente com 999 faltas no ano** e gente com faltas negativas. Antes de calcular qualquer média, decida qual faixa é possível — e escreva por quê. A média muda de 24 para 7 dias.

- **Ausente não é inválido.** Quem está com o campo de faltas vazio não faltou zero dias: você não sabe. São decisões diferentes.

- **Campo de desligamento vazio não é erro.** Significa que a pessoa ainda trabalha lá. Apagar essas linhas destrói a base.

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

URL = "https://raw.githubusercontent.com/SEU-USUARIO/atividades-casa-sig/main/2-rh-absenteismo/dados/ponto_rh.csv"
ponto = pd.read_csv(URL)

print(ponto.shape)
ponto.head()
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
