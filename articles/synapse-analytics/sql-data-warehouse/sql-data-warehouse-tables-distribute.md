---
title: Orientação de design de mesas distribuídas
description: Recomendações para a conceção de mesas distribuídas por haxixe e rodapé redondo utilizando piscina SQL dedicada em Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3b61df954e913671eafff4b739e0f53a4d420c28
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117304"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Orientação para o design de mesas distribuídas utilizando piscina SQL dedicada em Azure Synapse Analytics

Recomendações para a conceção de mesas distribuídas por haxixe e rodapé em piscinas SQL dedicadas.

Este artigo pressupõe que está familiarizado com os conceitos de distribuição de dados e movimento de dados em pool de SQL dedicado.  Para mais informações, consulte [a arquitetura Azure Synapse Analytics.](massively-parallel-processing-mpp-architecture.md)

## <a name="what-is-a-distributed-table"></a>O que é uma mesa distribuída?

Uma tabela distribuída aparece como uma única tabela, mas as linhas são realmente armazenadas em 60 distribuições. As linhas são distribuídas com um algoritmo de haxixe ou rodapé.  

**As tabelas distribuídas por haxixe melhoram** o desempenho da consulta em grandes tabelas de factos, e são o foco deste artigo. **As mesas de rodapé** são úteis para melhorar a velocidade de carregamento. Estas escolhas de design têm um impacto significativo na melhoria da consulta e do desempenho de carregamento.

Outra opção de armazenamento de mesa é replicar uma pequena mesa em todos os nós compute. Para obter mais informações, consulte [a orientação do Design para tabelas replicadas.](design-guidance-for-replicated-tables.md) Para escolher rapidamente entre as três opções, consulte as tabelas Distribuídas na visão geral das [tabelas.](sql-data-warehouse-tables-overview.md)

Como parte do design de mesa, compreenda o máximo possível sobre os seus dados e como os dados são consultados.  Por exemplo, considere estas questões:

- Qual é o tamanho da mesa?
- Com que frequência a mesa é refrescada?
- Tenho tabelas de fatos e dimensões numa piscina de SQL dedicada?

### <a name="hash-distributed"></a>Haxixe distribuído

Uma tabela distribuída por haxixe distribui linhas de mesa através dos nós computativos utilizando uma função de haxixe determinista para atribuir cada linha a uma [distribuição](massively-parallel-processing-mpp-architecture.md#distributions).

![Tabela distribuída](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Tabela distribuída")  

Uma vez que valores idênticos sempre têm a mesma distribuição, a SQL Analytics tem conhecimento incorporado das localizações da linha. No pool de SQL dedicado este conhecimento é usado para minimizar o movimento de dados durante as consultas, o que melhora o desempenho da consulta.

Mesas distribuídas por haxixe funcionam bem para grandes mesas de factos num esquema estelar. Podem ter um número muito grande de linhas e ainda alcançar um alto desempenho. Existem, naturalmente, algumas considerações de design que o ajudam a obter o desempenho que o sistema distribuído é projetado para fornecer. Escolher uma boa coluna de distribuição é uma consideração que é descrita neste artigo.

Considere usar uma tabela distribuída por haxixe quando:

- O tamanho da mesa no disco é superior a 2 GB.
- A tabela tem operações frequentes de inserção, atualização e eliminação.

### <a name="round-robin-distributed"></a>Rodada-robin distribuída

Uma mesa distribuída de rodapé distribui linhas de mesa uniformemente em todas as distribuições. A atribuição de linhas a distribuições é aleatória. Ao contrário das tabelas distribuídas por haxixe, não é garantido que as filas com valores iguais sejam atribuídas à mesma distribuição.

Como resultado, o sistema às vezes precisa invocar uma operação de movimento de dados para organizar melhor os seus dados antes que possa resolver uma consulta.  Este passo extra pode abrandar as suas consultas. Por exemplo, juntar-se a uma mesa de rodapé geralmente requer remodelar as linhas, o que é um sucesso de desempenho.

Considere usar a distribuição de rodapé para a sua mesa nos seguintes cenários:

- Quando começar como um simples ponto de partida, uma vez que é o padrão
- Se não houver uma chave de união óbvia
- Se não houver uma boa coluna de candidatos para a distribuição de haxixe
- Se a mesa não partilhar uma chave comum de junção com outras tabelas
- Se a junção é menos significativa do que outras juntas na consulta
- Quando a mesa é uma mesa de preparação temporária

Os [dados](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) do imposto de carga de Nova Iorque dão um exemplo de carregar dados numa mesa de paragem de robin redondo.

## <a name="choosing-a-distribution-column"></a>Escolher uma coluna de distribuição

Uma mesa distribuída por haxixe tem uma coluna de distribuição que é a chave de haxixe. Por exemplo, o seguinte código cria uma tabela distribuída por haxixe com o ProductKey como coluna de distribuição.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Os dados armazenados na coluna de distribuição podem ser atualizados. As atualizações aos dados na coluna de distribuição podem resultar numa operação de baralhar dados.

A escolha de uma coluna de distribuição é uma decisão de design importante, uma vez que os valores desta coluna determinam a forma como as linhas são distribuídas. A melhor escolha depende de vários fatores, e geralmente envolve trocas. Uma vez escolhida uma coluna de distribuição, não pode alterá-la.  

Se não escolheu a melhor coluna da primeira vez, pode utilizar a [TABELA COMO SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para recriar a tabela com uma coluna de distribuição diferente.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Escolha uma coluna de distribuição com dados que distribuam uniformemente

Para melhor desempenho, todas as distribuições devem ter aproximadamente o mesmo número de linhas. Quando uma ou mais distribuições têm um número desproporcional de linhas, algumas distribuições terminam a sua parte de uma consulta paralela antes de outras. Uma vez que a consulta não pode ser completada até que todas as distribuições tenham terminado o processamento, cada consulta é tão rápida quanto a distribuição mais lenta.

- Distorção de dados significa que os dados não são distribuídos uniformemente em todas as distribuições
- O processamento significa que algumas distribuições demoram mais tempo do que outras quando executam consultas paralelas. Isto pode acontecer quando os dados são distorcidos.
  
Para equilibrar o processamento paralelo, selecione uma coluna de distribuição que:

- **Tem muitos valores únicos.** A coluna pode ter alguns valores duplicados. No entanto, todas as linhas com o mesmo valor são atribuídas à mesma distribuição. Uma vez que existem 60 distribuições, a coluna deve ter pelo menos 60 valores únicos.  Normalmente, o número de valores únicos é muito maior.
- **Não tem NULLs, ou tem apenas alguns NULs.** Para um exemplo extremo, se todos os valores da coluna forem NUS, todas as linhas são atribuídas à mesma distribuição. Como resultado, o processamento de consultas é desviado para uma distribuição, e não beneficia de processamento paralelo.
- **Não é uma coluna de data.** Todos os dados para a mesma data aterram na mesma distribuição. Se vários utilizadores estiverem todos a filtrar na mesma data, então apenas 1 das 60 distribuições fazem todo o trabalho de processamento.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Escolha uma coluna de distribuição que minimize o movimento de dados

Para obter as consultas corretas os resultados da consulta podem mover dados de um nó compute para outro. O movimento de dados geralmente acontece quando as consultas têm juntas e agregações em tabelas distribuídas. Escolher uma coluna de distribuição que ajude a minimizar o movimento de dados é uma das estratégias mais importantes para otimizar o desempenho da sua piscina DE SQL dedicada.

Para minimizar o movimento de dados, selecione uma coluna de distribuição que:

- É usado em `JOIN` `GROUP BY` , e `DISTINCT` `OVER` `HAVING` cláusulas. Quando duas grandes tabelas de fatos têm juntas frequentes, o desempenho da consulta melhora quando distribui ambas as tabelas numa das colunas de junção.  Quando uma tabela não é utilizada em juntas, considere distribuir a mesa numa coluna que esteja frequentemente na `GROUP BY` cláusula.
- *Não* é usado em `WHERE` cláusulas. Isto poderia limitar a consulta a não funcionar em todas as distribuições.
- *Não* é uma coluna de encontros. ONDE as cláusulas filtram frequentemente por data.  Quando isto acontece, todo o processamento pode ser executado em apenas algumas distribuições.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>O que fazer quando nenhuma das colunas é uma boa coluna de distribuição

Se nenhuma das suas colunas tiver valores distintos suficientes para uma coluna de distribuição, pode criar uma nova coluna como um composto de um ou mais valores. Para evitar o movimento de dados durante a execução de consultas, utilize a coluna de distribuição composta como coluna de junção em consultas.

Uma vez que você projeta uma tabela distribuída por haxixe, o próximo passo é carregar dados na tabela.  Para obter orientação sobre o carregamento, consulte [a visão geral do carregamento](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Como dizer se a sua coluna de distribuição é uma boa escolha

Depois de os dados serem carregados numa tabela distribuída por haxixe, verifique se as linhas são distribuídas uniformemente pelas 60 distribuições. As linhas por distribuição podem variar até 10% sem um impacto notável no desempenho.

### <a name="determine-if-the-table-has-data-skew"></a>Determinar se a tabela tem distorção de dados

Uma forma rápida de verificar se existe um desvio de dados é utilizar [o DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). O seguinte código SQL devolve o número de linhas de tabela que são armazenadas em cada uma das 60 distribuições. Para um desempenho equilibrado, as linhas da sua mesa distribuída devem ser distribuídas uniformemente por todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Para identificar quais tabelas têm mais de 10% de distorção de dados:

1. Crie a vista dbo.vTableSizes que é mostrada no artigo de visão geral das [tabelas.](sql-data-warehouse-tables-overview.md#table-size-queries)  
2. Execute a seguinte consulta:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Verificar planos de consulta para movimento de dados

Uma boa coluna de distribuição permite que as juntas e agregações tenham o mínimo movimento de dados. Isto afeta a forma como as juntas devem ser escritas. Para obter o movimento mínimo de dados para uma junção em duas tabelas distribuídas por haxixe, uma das colunas de junção precisa ser a coluna de distribuição.  Quando duas tabelas distribuídas por haxixe se juntam a uma coluna de distribuição do mesmo tipo de dados, a junção não requer movimento de dados. As juntas podem utilizar colunas adicionais sem incorrer em movimento de dados.

Para evitar o movimento de dados durante uma junção:

- As tabelas envolvidas na junção devem ser distribuídas em **uma das** colunas participantes na junta.
- Os tipos de dados das colunas de junção devem coincidir entre ambas as tabelas.
- As colunas devem ser unidas a um operador igual.
- O tipo de junção pode não ser um `CROSS JOIN` .

Para ver se as consultas estão a experimentar o movimento de dados, pode olhar para o plano de consulta.  

## <a name="resolve-a-distribution-column-problem"></a>Resolver um problema de coluna de distribuição

Não é necessário resolver todos os casos de distorção de dados. Distribuir dados é uma questão de encontrar o equilíbrio certo entre minimizar o distorcer de dados e o movimento de dados. Nem sempre é possível minimizar tanto o desvio de dados como o movimento de dados. Por vezes, o benefício de ter o movimento mínimo de dados pode superar o impacto de ter distorções de dados.

Para decidir se deve resolver dados distorcidos numa tabela, deve entender o máximo possível sobre os volumes de dados e consultas na sua carga de trabalho. Pode utilizar os passos no artigo [de monitorização](sql-data-warehouse-manage-monitor.md) de consultas para monitorizar o impacto do desvio no desempenho da consulta. Especificamente, procure quanto tempo leva grandes consultas para completar em distribuições individuais.

Uma vez que não é possível alterar a coluna de distribuição numa tabela existente, a forma típica de resolver o distorcimento de dados é recriar a tabela com uma coluna de distribuição diferente.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Re-criar a tabela com uma nova coluna de distribuição

Este exemplo utiliza [o QUADRO CREATE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para recriar uma tabela com uma coluna de distribuição de haxixe diferente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Passos seguintes

Para criar uma tabela distribuída, utilize uma destas declarações:

- [CREATE TABLE (piscina SQL dedicada)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT (piscina SQL dedicada)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)