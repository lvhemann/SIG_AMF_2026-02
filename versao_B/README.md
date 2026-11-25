# Prova G2 — Versão B: Quanto custa o suporte de cada cliente?

Repositório com os dados e as regras de cálculo da prova. O enunciado, com a história da empresa e as perguntas, está na folha da prova.

**Ferramenta:** Power BI Desktop (Power Query, colunas, medida e relatório)

O diretor financeiro está preparando os preços de 2027: “Acho que o plano Básico custa mais em suporte do que deveria: o cliente paga pouco e abre muitos chamados. Minha proposta é reajustar o Básico em 20% para todos os clientes. Antes de decidir, quero saber quanto custa o suporte de cada cliente e se o problema é mesmo o plano.”

## Arquivos

Todos na pasta `dados/`. Os CSV usam **ponto e vírgula** como separador e codificação **UTF-8**.

| Arquivo | O que é | Linhas |
| --- | --- | --: |
| `chamados_2026_08.csv` | chamados abertos em agosto/2026 | 183 |
| `chamados_2026_09.csv` | chamados abertos em setembro/2026 | 205 |
| `chamados_2026_10.csv` | chamados abertos em outubro/2026 | 247 |
| `clientes.csv` | cadastro dos clientes | 28 |
| `categorias.csv` | cadastro das categorias de chamado | 7 |
| `analistas.csv` | cadastro dos analistas | 6 |

Use a coluna Linhas para conferir se a importação trouxe tudo.

## Dicionário de dados

**chamados\_2026\_MM.csv** — uma linha por chamado

| Coluna | Descrição |
| --- | --- |
| `id_chamado` | número do chamado |
| `data_abertura` | data de abertura (dd/mm/aaaa) |
| `cliente` | código do cliente (ver `clientes.csv`) |
| `categoria` | código da categoria (ver `categorias.csv`) |
| `analista` | código do analista que atendeu (ver `analistas.csv`) |
| `status` | `Resolvido`, `Cancelado` ou `Em aberto` |
| `horas_ate_solucao` | horas corridas entre a abertura e a solução: é o tempo que o cliente esperou (vazio se não foi resolvido) |
| `minutos_trabalhados` | tempo que o analista efetivamente trabalhou no chamado |

**clientes.csv** — `cliente`, `nome_cliente`, `segmento`, `cidade`, `plano` (Básico ou Completo) e `mensalidade` (R$ por mês).

**categorias.csv** — `categoria`, `nome_categoria`, `nivel_atendimento` (N1 ou N2) e `prazo_horas`.

**analistas.csv** — `analista`, `nome`, `nivel`, `vinculo`, `carga_horaria_mensal` (horas contratadas por mês) e `custo_mensal` (salário mais encargos, em R$).

## Regras de cálculo

Usem exatamente estas definições.

| Regra | Definição | Onde |
| --- | --- | --- |
| O que entra | só chamados com `status = Resolvido` | `chamados` |
| `custo_hora` | `custo_mensal` ÷ `carga_horaria_mensal` | `analistas` |
| `horas_trabalhadas` | `minutos_trabalhados` ÷ 60 | `chamados` |
| `custo_atendimento` | `horas_trabalhadas` × `custo_hora` | `chamados`, depois de mesclar com `analistas` |
| `mes` | mês da abertura (Adicionar Coluna → Data → Mês → Mês) | `chamados` |
| `chamados` e `custo_suporte` | Agrupar por `cliente`: Contar Linhas e Soma de `custo_atendimento` | consulta `suporte_cliente` |
| `receita_periodo` | `mensalidade` × 3 (os três meses) | `suporte_cliente` |
| `pct_suporte` | `custo_suporte` ÷ `receita_periodo` | `suporte_cliente` |
| `custo_por_chamado` | `custo_suporte` ÷ `chamados` | `suporte_cliente` |
| `faixa` | `pct_suporte` < 0,10 → "Normal"; < 0,25 → "Atenção"; senão "Crítico" | `suporte_cliente` |
| `% suporte` | medida: `DIVIDE(SUM(suporte_cliente[custo_suporte]), SUM(suporte_cliente[receita_periodo]))` | relatório |

## Roteiro mínimo no Power BI

1. Importar os três meses e juntar em uma consulta `chamados` (Acrescentar Consultas → Três ou mais tabelas).
2. Remover Duplicadas e filtrar `status` = Resolvido.
3. Importar `analistas`, `clientes` e `categorias`. Em `analistas`, criar `custo_hora` (Adicionar Coluna → Padrão → Dividir).
4. Mesclar `chamados` com `analistas`, `clientes` e `categorias` e trazer as colunas necessárias.
5. Criar `horas_trabalhadas`, `custo_atendimento` e `mes` em `chamados`.
6. Criar `suporte_cliente`: botão direito em `chamados` → Referência → Agrupar por `cliente`. Mesclar com `clientes` e criar `receita_periodo`, `pct_suporte`, `custo_por_chamado` e `faixa`.
7. Fechar e Aplicar, criar a medida e montar as matrizes pedidas na prova.

## Dúvidas frequentes

- **Tipos de coluna:** depois de importar, confira que `minutos_trabalhados`, `carga_horaria_mensal`, `custo_mensal` e `mensalidade` são número e que `data_abertura` é data.
- **Número decimal:** na Coluna Condicional, digite o número com vírgula (0,10 e 0,25).
- **Dividir e Multiplicar:** em Padrão → Dividir, a primeira coluna selecionada é a de cima da conta.
- **Mesclagem sem correspondência:** as colunas usadas como chave precisam ter o mesmo tipo nas duas tabelas.
- **As duas colunas de tempo:** custo de pessoal se calcula com o tempo trabalhado, não com o tempo que o cliente esperou.

## Entrega

O arquivo `.pbix` com as consultas, as colunas, a medida e os visuais, mais a folha da prova com as respostas escritas.

*Empresa, pessoas e números são fictícios, criados para esta prova.*
