# Trabalho SIG — Rendimento do Service Desk da Pampa Alimentos

**Disciplina:** G0397 — Sistemas de Informação Gerenciais 

**Ferramenta:** Power BI Desktop (Power Query + relatório)

**Formato:** em dupla

**Prazo:** 25/11

> Empresa, pessoas e números são fictícios, criados para este trabalho.

---

## 1. Contexto

A **Pampa Alimentos** é uma distribuidora com cerca de 1.200 funcionários em quatro locais: Matriz, Centro de Distribuição, Filial Santa Cruz e Filial Lajeado.

O suporte de TI é feito por um **service desk** interno com 7 pessoas:

- **N1** (primeiro nível): três analistas CLT e um estagiário. Atendem senha, impressora, e-mail, computador e instalação de programas.
- **N2** (segundo nível): dois analistas que atendem o sistema ERP.
- **Infra**: um analista de infraestrutura que cuida de rede e servidores.

Todo chamado é aberto em um sistema de chamados, e o técnico registra quantos minutos trabalhou para resolver. A presença de cada técnico é registrada no ponto eletrônico.

Em **1º de outubro** a empresa colocou em uso um módulo novo de faturamento no ERP.

## 2. A demanda

O gerente de TI vai apresentar o planejamento de 2027 à diretoria. Ele precisa responder:

> “A equipe de suporte está bem dimensionada? Quanto custa cada atendimento entregue, por técnico e por tipo de chamado? Alguém está sobrecarregado ou fazendo trabalho que não é do seu nível? Preciso de três recomendações com números.”

Vocês são a área de Sistemas de Informação que vai montar a análise no Power BI.

## 3. Arquivos

Todos os arquivos estão na pasta `dados/`. Os CSV usam **ponto e vírgula** como separador e codificação **UTF-8**.

| Arquivo | O que é | Linhas |
|---|---|---|
| `chamados_2026_08.csv` | chamados abertos em agosto/2026 | 886 |
| `chamados_2026_09.csv` | chamados abertos em setembro/2026 | 900 |
| `chamados_2026_10.csv` | chamados abertos em outubro/2026 | 1.155 |
| `tecnicos.csv` | cadastro dos técnicos | 7 |
| `categorias.csv` | cadastro das categorias de chamado | 7 |
| `ponto_horas.csv` | horas registradas no ponto, por técnico e mês | 21 |

Use os números da coluna "Linhas" para conferir se a importação trouxe tudo.

### 3.1 Dicionário de dados

**chamados_2026_MM.csv** — uma linha por chamado

| Coluna | Descrição |
|---|---|
| `id_chamado` | número do chamado |
| `data_abertura` | data em que o chamado foi aberto (dd/mm/aaaa) |
| `data_fechamento` | data em que foi resolvido (vazio se não foi resolvido) |
| `unidade` | local de quem abriu o chamado |
| `categoria` | código da categoria (ver `categorias.csv`) |
| `tecnico` | código do técnico que atendeu (ver `tecnicos.csv`) |
| `minutos_trabalhados` | tempo que o técnico trabalhou no chamado |
| `status` | `Resolvido`, `Cancelado` ou `Em aberto` |
| `avaliacao` | nota de 1 a 5 dada pelo usuário (vazio se ele não avaliou) |

**tecnicos.csv** — cadastro

| Coluna | Descrição |
|---|---|
| `tecnico` | código (T01 a T07) |
| `nome` | nome do técnico |
| `nivel` | N1, N2 ou Infra |
| `vinculo` | CLT 44h ou Estágio 30h |
| `carga_horaria_mensal` | horas contratadas por mês |
| `custo_mensal` | custo mensal para a empresa (salário + encargos), em R$ |

**categorias.csv** — cadastro

| Coluna | Descrição |
|---|---|
| `categoria` | código (SEN, IMP, EML, HW, SW, ERP, REDE) |
| `nome_categoria` | nome por extenso |
| `nivel_esperado` | nível de técnico que deveria atender essa categoria |
| `tempo_padrao_min` | tempo de referência para resolver, em minutos |
| `prazo_horas` | prazo de atendimento combinado com os usuários |

**ponto_horas.csv** — uma linha por técnico por mês

| Coluna | Descrição |
|---|---|
| `tecnico` | código do técnico |
| `mes` | número do mês (8, 9 ou 10) |
| `horas_registradas` | horas efetivamente trabalhadas no mês segundo o ponto (já descontadas faltas e férias) |

## 4. Regras de negócio (como calcular)

Estas são as definições combinadas com o gerente. Usem exatamente estas fórmulas.

| Indicador | Fórmula | Onde |
|---|---|---|
| `custo_hora` | custo_mensal ÷ carga_horaria_mensal | cadastro de técnicos |
| `horas_trabalhadas` | minutos_trabalhados ÷ 60 | cada chamado |
| `custo_atendimento` | horas_trabalhadas × custo_hora | cada chamado |
| `nivel_adequado` | “Sim” se o nível do técnico for igual ao `nivel_esperado` da categoria; senão “Não” | cada chamado |
| `entregas` | quantidade de chamados resolvidos | por técnico e mês |
| `horas_em_chamados` | soma de horas_trabalhadas | por técnico e mês |
| `utilizacao` | horas_em_chamados ÷ horas_registradas | por técnico e mês |
| `entregas_por_hora` | entregas ÷ horas_registradas | por técnico e mês |
| `custo_periodo` | horas_registradas × custo_hora | por técnico e mês |
| `custo_por_entrega` | custo_periodo ÷ entregas | por técnico e mês |
| `faixa_utilizacao` | menor que 50% → “Baixa”; menor que 85% → “Adequada”; senão “Sobrecarga” | por técnico e mês |

Só entram na análise os chamados com `status = Resolvido`. Chamados cancelados não geram entrega, e chamados em aberto ainda não foram concluídos.

## 5. O que fazer no Power BI

Este roteiro é o mínimo esperado. Os comandos são os mesmos da aula da loja de calçados, mais o **Agrupar por** e a **mesclagem por duas colunas**.

1. **Importar e juntar os três meses** de chamados em uma consulta `chamados` (Acrescentar Consultas → Três ou mais tabelas).
2. **Filtrar** a coluna `status`, deixando só `Resolvido`.
3. **Importar os cadastros** `tecnicos.csv`, `categorias.csv` e `ponto_horas.csv`. Em `tecnicos`, criar a coluna `custo_hora` (Adicionar Coluna → Padrão → Dividir).
4. **Mesclar** `chamados` com `tecnicos` (pela coluna `tecnico`) e com `categorias` (pela coluna `categoria`). Trazer as colunas necessárias.
5. **Criar as colunas por chamado**: `horas_trabalhadas`, `custo_atendimento`, `nivel_adequado` (Coluna Condicional comparando duas colunas) e o mês da abertura (Adicionar Coluna → Data → Mês → Mês).
6. **Criar a consulta de rendimento**: botão direito em `chamados` → Referência → Agrupar por `tecnico` e `mes`, com `entregas` (Contar Linhas) e `horas_em_chamados` (Soma).
7. **Mesclar** o rendimento com `ponto_horas` usando **duas colunas** (`tecnico` e `mes`, segurando Ctrl) e com `tecnicos`. Criar `utilizacao`, `entregas_por_hora`, `custo_periodo`, `custo_por_entrega` e `faixa_utilizacao`.
8. **Fechar e Aplicar** e montar, no mínimo:
   - uma matriz técnico × mês com entregas, utilização e custo por entrega;
   - uma matriz categoria × mês com quantidade de chamados e custo de atendimento;
   - uma visão dos chamados com `nivel_adequado = Não` (quem, qual categoria, quantas horas, quanto custou).
9. **Criar pelo menos uma medida** em DAX. Sugestão: utilização da equipe = soma das horas em chamados ÷ soma das horas registradas, usando `DIVIDE`.

## 6. Perguntas que o relatório deve responder

1. Quanto a equipe custou por mês e quanto desse custo virou atendimento de chamado?
2. Qual é a utilização de cada técnico em cada mês? Quem está com utilização baixa e quem está em sobrecarga?
3. Qual o custo por entrega de cada técnico? É justo comparar o custo por entrega de um N1 com o de um N2? Por quê?
4. O que mudou em outubro? Que categoria e que nível de técnico foram afetados?
5. Existe trabalho sendo feito por um nível diferente do esperado? Quantas horas e quanto custou?
6. Algum indicador pode levar a uma conclusão errada se for lido sozinho? Dê um exemplo dos dados.

## 7. Entrega

Entregar, no ambiente indicado pelo professor, **um arquivo .zip** com:

1. O arquivo **.pbix** com as consultas, as colunas, a medida e os visuais.
2. Um **relatório em PDF de no máximo 2 páginas** com:
   - os prints dos visuais principais;
   - a resposta às 6 perguntas da seção 6;
   - **três recomendações** ao gerente de TI, cada uma com o número que a sustenta.
3. O **nome e RA** dos dois integrantes na primeira página do PDF.

## 8. Critérios de avaliação

| Critério | Peso |
|---|---|
| Consultas corretas: três meses juntos, filtro de status, mesclagens com os cadastros | 20% |
| Colunas calculadas conforme as regras da seção 4 | 25% |
| Consulta de rendimento (Agrupar por + mesclagem por duas colunas) | 20% |
| Visuais e medida | 10% |
| Respostas às perguntas e recomendações sustentadas por números | 25% |

## 9. Dúvidas frequentes

- **O Power BI mudou o tipo da coluna sozinho.** Confira os tipos depois de importar: `minutos_trabalhados`, `carga_horaria_mensal`, `custo_mensal` e `horas_registradas` devem ser número; as datas devem ser data.
- **A mesclagem não encontrou correspondência.** As colunas usadas como chave precisam ter o mesmo tipo nas duas tabelas (por exemplo, `mes` como número inteiro nas duas).
- **Para mesclar por duas colunas**, clique na primeira, segure **Ctrl** e clique na segunda, na mesma ordem nas duas tabelas.
- **Subtrair e Dividir:** a primeira coluna selecionada é a de cima da conta.
- **Utilização baixa não significa, sozinha, que alguém está sem trabalho.** Pensem no que o sistema de chamados registra e no que ele não registra.

