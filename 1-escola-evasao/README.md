# Atividade 1 — Escola Técnica Aurora
## Matrículas e evasão

**Atividade de casa 1 de 6 · limpeza básica**
Disciplina **G0397 — Sistemas de Informação Gerenciais** · AMF Faculdade
Conteúdo de apoio: Aula 03 — tratamento de uma tabela

---

## A situação

A Escola Técnica Aurora fechou mais um semestre. A direção sabe que perde
aluno pelo caminho, mas não sabe **onde**: cada evasão é uma vaga que ficou paga pela
metade e uma sala com cadeira vazia até o fim do curso.

A secretaria exportou as matrículas dos últimos três semestres e mandou o arquivo por
e-mail. Ninguém revisou. Foi digitado por três secretárias diferentes, em turnos
diferentes, ao longo de um ano e meio.

**Seu papel:** você é a pessoa chamada para transformar esse arquivo em resposta.

---

## Os dados

`dados/matriculas_escola.csv` — 384 linhas · 8 colunas

| Coluna | O que é | O que se espera |
|---|---|---|
| `matricula_id` | número da matrícula | inteiro, único |
| `aluno` | nome do aluno | nome e sobrenome |
| `responsavel` | responsável pela matrícula | nome e sobrenome |
| `curso` | curso técnico | um dos 7 cursos da escola |
| `turno` | turno das aulas | Manhã, Tarde ou Noite |
| `situacao` | situação do aluno | Cursando, Concluído ou Evadido |
| `mensalidade` | valor da mensalidade | em reais |
| `data_matricula` | quando se matriculou | data |

> A coluna "o que se espera" descreve o **mundo ideal**. O arquivo real é outra história.

---

## As três perguntas

1. **Qual é a taxa de evasão da escola?** E como ela varia por curso?

2. **Qual turno concentra a evasão?** A escola tem mais aula à noite — isso ajuda ou atrapalha?

3. **Quantos alunos distintos** a escola atendeu de fato no período?

---

## Por onde começar

1. **`aluno`** — tirar os espaços sobrando (`str.strip()`). É o que faz a contagem de alunos distintos ficar certa.
2. **`curso`** — padronizar: `str.strip().str.upper()` e depois um `replace()` com dicionário. Depois do `upper()` sobram 15 grafias para 7 cursos.
3. **`situacao`** — a mesma coisa: 4 grafias para 3 situações.
4. **duplicatas** — a mesma pessoa, no mesmo curso, na mesma data: é rematrícula digitada duas vezes.

**As demais colunas já vêm limpas.** Não perca tempo com elas.

---

## Armadilhas desta base

- **Taxa não é contagem.** Administração tem o maior número de evasões, mas também é o maior curso. O curso com a pior *taxa* é outro — e é essa a pergunta.

- **Rematrícula digitada duas vezes não é aluno novo.** Decida o que conta como duplicata antes de contar qualquer coisa.

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

URL = "https://raw.githubusercontent.com/SEU-USUARIO/atividades-casa-sig/main/1-escola-evasao/dados/matriculas_escola.csv"
matriculas = pd.read_csv(URL)

print(matriculas.shape)
matriculas.head()
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
