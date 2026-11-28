# Prova G2 — Versão C: Em qual segmento fazer a campanha de vendas?

Repositório com os dados e as regras de cálculo da prova. O enunciado, com a história da empresa e as perguntas, está na folha da prova.

**Ferramenta:** Power BI Desktop (Power Query, colunas, medidas e relatório)

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
| `horas_ate_solucao` | horas corridas entre a abertura e a solução (vazio se não foi resolvido) |
| `minutos_trabalhados` | tempo que o analista trabalhou no chamado |

**clientes.csv** — `cliente`, `nome_cliente`, `segmento`, `cidade`, `plano` (Básico ou Completo) e `mensalidade` (R$ por mês).

**categorias.csv** — `categoria`, `nome_categoria`, `nivel_atendimento` (N1 ou N2) e `prazo_horas` (prazo de solução combinado com os clientes).

**analistas.csv** — `analista`, `nome`, `nivel`, `vinculo`, `carga_horaria_mensal` (horas contratadas por mês) e `custo_mensal` (salário mais encargos, em R$).

## Regras de cálculo

Usem exatamente estas definições.

| Regra | Definição | Onde |
| --- | --- | --- |
| O que entra | só chamados com `status = Resolvido` | `chamados` |
| `custo_hora` | `custo_mensal` ÷ `carga_horaria_mensal` | `analistas` |
| `horas_trabalhadas` | `minutos_trabalhados` ÷ 60 | `chamados` |
| `custo_atendimento` | `horas_trabalhadas` × `custo_hora` | `chamados`, depois de mesclar com `analistas` |
| `no_prazo` | 1 se `horas_ate_solucao` ≤ `prazo_horas`; senão 0 | `chamados`, depois de mesclar com `categorias` |
| `chamados`, `custo_suporte`, `chamados_no_prazo` | Agrupar por `cliente`: Contar Linhas, Soma de `custo_atendimento`, Soma de `no_prazo` | consulta `resultado_cliente` |
| `receita_periodo` | `mensalidade` × 3 (os três meses) | `resultado_cliente`, depois de mesclar com `clientes` |
| `margem` | `receita_periodo` − `custo_suporte` | `resultado_cliente` |

**Medidas.** As duas primeiras na tabela `chamados`; as outras na tabela `resultado_cliente`.

```
Chamados = COUNTROWS(chamados)
Custo de suporte = SUM(chamados[custo_atendimento])
Clientes = COUNTROWS(resultado_cliente)
Margem total = SUM(resultado_cliente[margem])
Margem por cliente = DIVIDE(SUM(resultado_cliente[margem]), COUNTROWS(resultado_cliente))
% no prazo = DIVIDE(SUM(resultado_cliente[chamados_no_prazo]), SUM(resultado_cliente[chamados]))
Chamados por cliente = DIVIDE(SUM(resultado_cliente[chamados]), COUNTROWS(resultado_cliente))
```

A meta da empresa é **90% dos chamados no prazo**. Todo cliente novo entra no plano **Básico** no primeiro ano.

## Roteiro mínimo no Power BI

1. Importar os três meses e juntar em uma consulta `chamados` (Acrescentar Consultas → Três ou mais tabelas).
2. Remover Duplicadas e filtrar `status` = Resolvido.
3. Importar `analistas`, `categorias` e `clientes`. Em `analistas`, criar `custo_hora` (Adicionar Coluna → Padrão → Dividir).
4. Mesclar `chamados` com `analistas` (trazer `custo_hora`) e com `categorias` (trazer `prazo_horas`).
5. Criar `horas_trabalhadas`, `custo_atendimento` e `no_prazo` em `chamados`.
6. Criar `resultado_cliente`: botão direito em `chamados` → Referência → Agrupar por `cliente` (Avançado, três agregações). Mesclar com `clientes` e trazer `nome_cliente`, `segmento`, `plano` e `mensalidade`. Criar `receita_periodo` e `margem`.
7. Fechar e Aplicar, criar as medidas e montar o painel abaixo.

## Como criar a consulta por Referência

Uma consulta por **Referência** é uma consulta nova que começa do resultado de outra. Aqui ela começa de `chamados`: tudo o que foi feito em `chamados` (juntar os meses, remover duplicadas, filtrar, mesclar, criar colunas) já vem pronto, e se você mudar algo em `chamados` depois, a consulta nova recebe a mudança. A consulta `chamados` continua inteira, com um chamado por linha, para as outras matrizes.

**Antes de começar:** termine as colunas de `chamados` (`horas_trabalhadas`, `custo_atendimento` e `no_prazo`).

1. No Power Query, no painel **Consultas** (à esquerda), clique com o botão direito em `chamados` → **Referência**.
2. Aparece uma consulta nova chamada `chamados (2)`. Clique com o botão direito nela → **Renomear** → `resultado_cliente`.
3. Confira em **Etapas Aplicadas** (à direita): a primeira etapa é `Fonte`, e na barra de fórmulas aparece `= chamados`. Se aparecerem as etapas de `chamados` repetidas, você usou Duplicar no lugar de Referência: apague a consulta e comece de novo.
4. Com `resultado_cliente` selecionada: Página Inicial → **Agrupar por** → marque **Avançado**.
5. Em "Agrupar por", escolha `cliente`. Preencha as agregações (use **Adicionar agregação** para cada linha nova):

   | Nome da nova coluna | Operação | Coluna |
   | --- | --- | --- |
   | `chamados` | Contar Linhas | (não precisa) |
   | `custo_suporte` | Soma | `custo_atendimento` |
   | `chamados_no_prazo` | Soma | `no_prazo` |

6. OK. A consulta passa a ter **uma linha por cliente**.
7. Página Inicial → **Mesclar Consultas** → tabela `clientes` → clique em `cliente` nas duas tabelas → Externa esquerda → OK. Expanda e traga `nome_cliente`, `segmento`, `plano` e `mensalidade`.
8. Crie `receita_periodo` e `margem` (tabela de regras).

**Erros comuns**

- **Usar Duplicar no lugar de Referência:** a cópia não acompanha as mudanças feitas depois em `chamados`.
- **Fazer o Agrupar por dentro da própria `chamados`:** a tabela de chamados deixa de ter um chamado por linha, e as matrizes que usam `chamados` passam a mostrar números errados. O Agrupar por vai sempre na consulta nova.
- **Escolher Contar Linhas Distintas:** para contar chamados, a operação é **Contar Linhas**.

## O que o painel precisa ter (mínimo)

O `.pbix` entregue precisa ter as consultas, as colunas e as medidas das regras acima e **duas páginas** com os visuais abaixo. Cada visual precisa de um título que diga o que ele mostra. Percentuais formatados como %, valores em reais com duas casas decimais.

### Página 1 — Clientes

| Visual | Campos |
| --- | --- |
| Cartões | chamados resolvidos · soma de `custo_atendimento` · `% no prazo` · `Margem por cliente` |
| Tabela de clientes | `nome_cliente`, `segmento`, `plano`, `chamados`, `custo_suporte`, `receita_periodo`, `margem` · ordenada pela `margem` |
| Matriz segmento × plano | linhas: `segmento` · colunas: `plano` · valor: quantidade de clientes |

### Página 2 — Segmentos

| Visual | Campos |
| --- | --- |
| Matriz por segmento | linhas: `segmento` · valores: quantidade de clientes, `Margem por cliente`, `% no prazo`, `Chamados por cliente` |
| Matriz por plano | linhas: `plano` · valor: `Margem por cliente` |
| Segmentação | `plano` (para ver só o Básico no item 7) |

Não é preciso: tema de cores, imagens ou layout elaborado. Conta o que está certo e legível.

## Dúvidas frequentes

- **Tipos de coluna:** depois de importar, confira que `horas_ate_solucao`, `minutos_trabalhados`, `prazo_horas`, `carga_horaria_mensal`, `custo_mensal` e `mensalidade` são número e que `data_abertura` é data.
- **Número decimal:** os CSV usam vírgula como separador decimal (por exemplo, 12,5).
- **Dividir e Subtrair:** em Padrão, a primeira coluna selecionada é a de cima da conta.
- **Agrupar por com várias colunas:** use a opção Avançado da janela Agrupar por para criar as três agregações de uma vez.
- **Mesclagem sem correspondência:** as colunas usadas como chave precisam ter o mesmo tipo nas duas tabelas.

## Entrega

O arquivo `.pbix` com as consultas, as colunas, as medidas e os visuais, mais a folha da prova com as respostas escritas.

*Empresa, pessoas e números são fictícios, criados para esta prova.*
