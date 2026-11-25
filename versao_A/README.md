# Prova G2 — Versão A: O suporte está cumprindo o prazo?

Repositório com os dados e as regras de cálculo da prova. O enunciado, com a história da empresa e as perguntas, está na folha da prova.

**Ferramenta:** Power BI Desktop (Power Query, colunas, medida e relatório)

## Arquivos

Todos na pasta `dados/`. Os CSV usam **ponto e vírgula** como separador e codificação **UTF-8**.

| Arquivo | O que é | Linhas |
| --- | --- | --: |
| `chamados_2026_08.csv` | chamados abertos em agosto/2026 | 183 |
| `chamados_2026_09.csv` | chamados abertos em setembro/2026 | 205 |
| `chamados_2026_10.csv` | chamados abertos em outubro/2026 | 247 |
| `clientes.csv` | cadastro dos clientes | 28 |
| `categorias.csv` | cadastro das categorias de chamado | 7 |

Use a coluna Linhas para conferir se a importação trouxe tudo.

## Dicionário de dados

**chamados\_2026\_MM.csv** — uma linha por chamado

| Coluna | Descrição |
| --- | --- |
| `id_chamado` | número do chamado |
| `data_abertura` | data de abertura (dd/mm/aaaa) |
| `cliente` | código do cliente (ver `clientes.csv`) |
| `categoria` | código da categoria (ver `categorias.csv`) |
| `analista` | código do analista que atendeu |
| `status` | `Resolvido`, `Cancelado` ou `Em aberto` |
| `horas_ate_solucao` | horas corridas entre a abertura e a solução (vazio se não foi resolvido) |
| `minutos_trabalhados` | tempo que o analista trabalhou no chamado |

**clientes.csv** — `cliente`, `nome_cliente`, `segmento`, `cidade`, `plano` (Básico ou Completo) e `mensalidade` (R$ por mês).

**categorias.csv** — `categoria`, `nome_categoria`, `nivel_atendimento` (N1 ou N2) e `prazo_horas` (prazo de solução combinado com os clientes).

## Regras de cálculo

Usem exatamente estas definições.

| Regra | Definição | Como fazer no Power BI |
| --- | --- | --- |
| O que entra | só chamados com `status = Resolvido` | filtro na coluna `status` |
| `no_prazo` | 1 se `horas_ate_solucao` ≤ `prazo_horas`; senão 0 | Coluna Condicional, comparando com a coluna `prazo_horas` |
| `% no prazo` | soma de `no_prazo` ÷ quantidade de chamados | medida: `DIVIDE(SUM(chamados[no_prazo]), COUNTROWS(chamados))` |
| atrasados | chamados − soma de `no_prazo` | — |
| `dia_semana` | nome do dia da abertura | Adicionar Coluna → Data → Dia → Nome do Dia |
| `tipo_dia` | "Sábado" se `dia_semana` = sábado; senão "Dia útil" | Coluna Condicional |
| `mes` | mês da abertura | Adicionar Coluna → Data → Mês → Mês |
| `horas_alem_do_prazo` | `horas_ate_solucao` − `prazo_horas` (negativo = sobrou tempo; positivo = passou do prazo) | Padrão → Subtrair, selecionando primeiro `horas_ate_solucao` |

A meta da empresa é **90% dos chamados no prazo**. O suporte atende de segunda a sexta, das 8h às 18h.

## Roteiro mínimo no Power BI

1. Importar os três meses e juntar em uma consulta `chamados` (Acrescentar Consultas → Três ou mais tabelas).
2. Remover Duplicadas e filtrar `status` = Resolvido.
3. Importar `clientes` e `categorias` e mesclar com `chamados` pelas colunas `cliente` e `categoria`.
4. Criar as colunas e a medida da tabela de regras.
5. Para os clientes do sábado: Referência de `chamados`, filtrar `tipo_dia` = Sábado, Agrupar por `cliente` (Contar Linhas) e mesclar com `clientes`.
6. Fechar e Aplicar e montar as matrizes pedidas na prova.

## Dúvidas frequentes

- **Tipos de coluna:** depois de importar, confira que `horas_ate_solucao`, `minutos_trabalhados`, `prazo_horas` e `mensalidade` são número e que `data_abertura` é data.
- **Número decimal:** os CSV usam vírgula como separador decimal (por exemplo, 12,5). Na Coluna Condicional, digite o número com vírgula.
- **Subtrair:** a primeira coluna selecionada é a de cima da conta.
- **Mesclagem sem correspondência:** as colunas usadas como chave precisam ter o mesmo tipo nas duas tabelas.

## Entrega

O arquivo `.pbix` com as consultas, as colunas, a medida e os visuais, mais a folha da prova com as respostas escritas.

*Empresa, pessoas e números são fictícios, criados para esta prova.*
