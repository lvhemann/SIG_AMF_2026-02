# Prova G2 — Versão D: Qual melhoria do sistema se paga mais rápido?

Repositório com os dados e as regras de cálculo da prova. O enunciado, com a história da empresa, as propostas de melhoria e as perguntas, está na folha da prova.

**Ferramenta:** Power BI Desktop (Power Query, colunas, medidas e relatório)

## Arquivos

Todos na pasta `dados/`. Os CSV usam **ponto e vírgula** como separador e codificação **UTF-8**.

| Arquivo | O que é | Linhas |
| --- | --- | --: |
| `chamados_2026_08.csv` | chamados abertos em agosto/2026 | 183 |
| `chamados_2026_09.csv` | chamados abertos em setembro/2026 | 205 |
| `chamados_2026_10.csv` | chamados abertos em outubro/2026 | 247 |
| `categorias.csv` | cadastro das categorias de chamado | 7 |
| `analistas.csv` | cadastro dos analistas | 6 |

Use a coluna Linhas para conferir se a importação trouxe tudo.

## Dicionário de dados

**chamados\_2026\_MM.csv** — uma linha por chamado

| Coluna | Descrição |
| --- | --- |
| `id_chamado` | número do chamado |
| `data_abertura` | data de abertura (dd/mm/aaaa) |
| `cliente` | código do cliente |
| `categoria` | código da categoria (ver `categorias.csv`) |
| `analista` | código do analista que atendeu (ver `analistas.csv`) |
| `status` | `Resolvido`, `Cancelado` ou `Em aberto` |
| `horas_ate_solucao` | horas corridas entre a abertura e a solução: é o tempo que o cliente esperou (vazio se não foi resolvido) |
| `minutos_trabalhados` | tempo que o analista efetivamente trabalhou no chamado |

**categorias.csv** — `categoria`, `nome_categoria`, `nivel_atendimento` (N1 ou N2: o nível que atende essa categoria) e `prazo_horas`.

**analistas.csv** — `analista`, `nome`, `nivel`, `vinculo`, `carga_horaria_mensal` (horas contratadas por mês) e `custo_mensal` (salário mais encargos, em R$).

## Regras de cálculo

Usem exatamente estas definições.

| Regra | Definição | Onde |
| --- | --- | --- |
| O que entra | só chamados com `status = Resolvido` | `chamados` |
| `custo_hora` | `custo_mensal` ÷ `carga_horaria_mensal` | `analistas` |
| `horas_trabalhadas` | `minutos_trabalhados` ÷ 60 | `chamados` |
| `custo_atendimento` | `horas_trabalhadas` × `custo_hora` | `chamados`, depois de mesclar com `analistas` |
| `chamados`, `horas`, `custo` | Agrupar por `nome_categoria`: Contar Linhas, Soma de `horas_trabalhadas`, Soma de `custo_atendimento` | consulta `custo_categoria` |
| `custo_mensal_categoria` | `custo` ÷ 3 (média dos três meses) | `custo_categoria` |
| `custo_medio_chamado` | `custo` ÷ `chamados` | `custo_categoria` |

**Medidas.** As quatro primeiras na tabela `chamados`; as duas últimas na tabela `custo_categoria`.

```
Chamados = COUNTROWS(chamados)
Custo de suporte = SUM(chamados[custo_atendimento])
Horas trabalhadas = SUM(chamados[horas_trabalhadas])
Custo por hora de atendimento = DIVIDE([Custo de suporte], [Horas trabalhadas])
% dos chamados = DIVIDE(SUM(custo_categoria[chamados]), CALCULATE(SUM(custo_categoria[chamados]), ALL(custo_categoria)))
% do custo = DIVIDE(SUM(custo_categoria[custo]), CALCULATE(SUM(custo_categoria[custo]), ALL(custo_categoria)))
```

`ALL` faz a medida dividir pelo total de todas as categorias, mesmo quando a matriz mostra uma categoria por linha.

**Contas da Parte 2** (com os números da tabela de propostas da prova):

- economia mensal = `custo_mensal_categoria` × redução esperada
- meses para se pagar = investimento ÷ economia mensal
- horas liberadas por mês = (`horas` ÷ 3) × redução esperada

## Roteiro mínimo no Power BI

1. Importar os três meses e juntar em uma consulta `chamados` (Acrescentar Consultas → Três ou mais tabelas).
2. Remover Duplicadas e filtrar `status` = Resolvido.
3. Importar `analistas` e `categorias`. Em `analistas`, criar `custo_hora` (Adicionar Coluna → Padrão → Dividir).
4. Mesclar `chamados` com `analistas` (trazer `nivel` e `custo_hora`) e com `categorias` (trazer `nome_categoria` e `nivel_atendimento`).
5. Criar `horas_trabalhadas` e `custo_atendimento` em `chamados`.
6. Criar `custo_categoria`: botão direito em `chamados` → Referência → Agrupar por `nome_categoria` (Avançado, três agregações). Criar `custo_mensal_categoria` e `custo_medio_chamado`.
7. Fechar e Aplicar, criar as medidas e montar o painel abaixo.

## Como criar a consulta por Referência

Uma consulta por **Referência** é uma consulta nova que começa do resultado de outra. Aqui ela começa de `chamados`: tudo o que foi feito em `chamados` (juntar os meses, remover duplicadas, filtrar, mesclar, criar colunas) já vem pronto, e se você mudar algo em `chamados` depois, a consulta nova recebe a mudança. A consulta `chamados` continua inteira, com um chamado por linha, para as outras matrizes.

**Antes de começar:** termine as colunas de `chamados` (`horas_trabalhadas` e `custo_atendimento`) e a mesclagem com `categorias`.

1. No Power Query, no painel **Consultas** (à esquerda), clique com o botão direito em `chamados` → **Referência**.
2. Aparece uma consulta nova chamada `chamados (2)`. Clique com o botão direito nela → **Renomear** → `custo_categoria`.
3. Confira em **Etapas Aplicadas** (à direita): a primeira etapa é `Fonte`, e na barra de fórmulas aparece `= chamados`. Se aparecerem as etapas de `chamados` repetidas, você usou Duplicar no lugar de Referência: apague a consulta e comece de novo.
4. Com `custo_categoria` selecionada: Página Inicial → **Agrupar por** → marque **Avançado**.
5. Em "Agrupar por", escolha `nome_categoria`. Preencha as agregações (use **Adicionar agregação** para cada linha nova):

   | Nome da nova coluna | Operação | Coluna |
   | --- | --- | --- |
   | `chamados` | Contar Linhas | (não precisa) |
   | `horas` | Soma | `horas_trabalhadas` |
   | `custo` | Soma | `custo_atendimento` |

6. OK. A consulta passa a ter **uma linha por categoria**.
7. Crie `custo_mensal_categoria` e `custo_medio_chamado` (tabela de regras).

**Erros comuns**

- **Usar Duplicar no lugar de Referência:** a cópia não acompanha as mudanças feitas depois em `chamados`.
- **Fazer o Agrupar por dentro da própria `chamados`:** a tabela de chamados deixa de ter um chamado por linha, e as matrizes que usam `chamados` passam a mostrar números errados. O Agrupar por vai sempre na consulta nova.
- **Escolher Contar Linhas Distintas:** para contar chamados, a operação é **Contar Linhas**.

## O que o painel precisa ter (mínimo)

O `.pbix` entregue precisa ter as consultas, as colunas e as medidas das regras acima e **duas páginas** com os visuais abaixo. Cada visual precisa de um título que diga o que ele mostra. Percentuais formatados como %, valores em reais com duas casas decimais.

### Página 1 — Categorias

| Visual | Campos |
| --- | --- |
| Cartões | chamados resolvidos · soma de `custo_atendimento` · soma de `horas_trabalhadas` |
| Tabela de categorias | da consulta `custo_categoria`: `nome_categoria`, `chamados`, `horas`, `custo`, `custo_mensal_categoria`, `custo_medio_chamado` · ordenada pelo `custo` |
| Matriz % chamados × % custo | linhas: `nome_categoria` · valores: `% dos chamados`, `% do custo` |
| Gráfico de barras | eixo: `nome_categoria` · valores: `% dos chamados` e `% do custo` lado a lado |

### Página 2 — Nível

| Visual | Campos |
| --- | --- |
| Matriz por nível | da consulta `chamados`: linhas `nivel_atendimento` · valores: `Chamados` e `Custo de suporte` |

Não é preciso: tema de cores, imagens ou layout elaborado. Conta o que está certo e legível.

## Dúvidas frequentes

- **Tipos de coluna:** depois de importar, confira que `minutos_trabalhados`, `carga_horaria_mensal` e `custo_mensal` são número e que `data_abertura` é data.
- **Número decimal:** os CSV usam vírgula como separador decimal (por exemplo, 12,5).
- **Dividir:** em Padrão → Dividir, a primeira coluna selecionada é a de cima da conta.
- **Agrupar por com várias colunas:** use a opção Avançado da janela Agrupar por para criar as três agregações de uma vez.
- **As duas colunas de tempo:** custo de pessoal se calcula com o tempo trabalhado, não com o tempo que o cliente esperou.

## Entrega

O arquivo `.pbix` com as consultas, as colunas, as medidas e os visuais, mais a folha da prova com as respostas escritas.

*Empresa, pessoas e números são fictícios, criados para esta prova.*
