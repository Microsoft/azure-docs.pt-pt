---
title: Orientação de design de tabelas distribuídas
description: Recomendações para a conceção de mesas distribuídas por haxixe e rodapé distribuídos em piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a93f3ada8e56853b78321bdc7d99a667cee6158
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583509"
---
# <a name="guidance-for-designing-distributed-tables-in-synapse-sql-pool"></a>Orientação para conceber tabelas distribuídas na piscina SQL synapse

Recomendações para a conceção de mesas distribuídas por haxixe e rodapé distribuídos em piscinas Synapse SQL.

Este artigo assume que está familiarizado com os conceitos de distribuição de dados e movimento de dados no pool SQL synapse.Para mais informações, consulte [a Azure Synapse Analytics em grande paralelo de processamento (MPP).](massively-parallel-processing-mpp-architecture.md) 

## <a name="what-is-a-distributed-table"></a>O que é uma mesa distribuída?

Uma tabela distribuída aparece como uma única mesa, mas as filas são realmente armazenadas em 60 distribuições. As linhas são distribuídas com um algoritmo de hash ou robin redondo.  

**As tabelas distribuídas por hash** melhoram o desempenho da consulta em grandes tabelas de factos, e são o foco deste artigo. **As mesas de rodapé** são úteis para melhorar a velocidade de carregamento. Estas escolhas de design têm um impacto significativo na melhoria da consulta e do desempenho de carregamento.

Outra opção de armazenamento de mesa é replicar uma pequena tabela em todos os nós computados. Para obter mais informações, consulte [a orientação do Design para tabelas replicadas](design-guidance-for-replicated-tables.md). Para escolher rapidamente entre as três opções, consulte tabelas distribuídas na visão geral das [tabelas](sql-data-warehouse-tables-overview.md). 

Como parte do design de tabela, compreenda o máximo possível sobre os seus dados e como os dados são consultados.Por exemplo, considere estas questões:

- Qual é o tamanho da mesa?   
- Quantas vezes a mesa é refrescada?   
- Tenho tabelas de fatos e dimensões numa piscina Synapse SQL?   


### <a name="hash-distributed"></a>Hash distribuído

Uma tabela distribuída por hash distribui linhas de mesa através dos nós computacionais utilizando uma função de hash determinista para atribuir cada linha a uma [distribuição](massively-parallel-processing-mpp-architecture.md#distributions). 

![Mesa distribuída](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Mesa distribuída")  

Uma vez que valores idênticos sempre têm a mesma distribuição, o armazém de dados tem conhecimento incorporado das localizações da linha. No pool SYnapse SQL este conhecimento é usado para minimizar o movimento de dados durante as consultas, o que melhora o desempenho da consulta. 

As mesas distribuídas por hash funcionam bem para grandes mesas de facto saem num esquema estelar. Podem ter um grande número de linhas e ainda alcançar um alto desempenho. Existem, naturalmente, algumas considerações de design que o ajudam a obter o desempenho que o sistema distribuído foi projetado para fornecer. Escolher uma boa coluna de distribuição é uma consideração que é descrita neste artigo. 

Considere utilizar uma tabela distribuída por hash quando:

- O tamanho da mesa no disco é superior a 2 GB.
- A tabela tem operações frequentes de inserção, atualização e exclusão. 

### <a name="round-robin-distributed"></a>Robin redondo distribuído

Uma mesa distribuída por robin redondo distribui linhas de mesa uniformemente em todas as distribuições. A atribuição de filas para distribuição é aleatória. Ao contrário das tabelas distribuídas por hash, as filas com valores iguais não estão garantidas para serem atribuídas à mesma distribuição. 

Como resultado, o sistema às vezes precisa invocar uma operação de movimento de dados para organizar melhor os seus dados antes que possa resolver uma consulta.  Este passo extra pode abrandar as suas consultas. Por exemplo, juntar-se a uma mesa de robin redondo geralmente requer remodelação das linhas, o que é um sucesso de desempenho.

Considere utilizar a distribuição de robin redondo para a sua tabela nos seguintes cenários:

- Quando começar como um simples ponto de partida, uma vez que é o padrão
- Se não houver uma chave de adesão óbvia
- Se não houver uma boa coluna de candidatos para o hash distribuir a tabela
- Se a tabela não partilhar uma chave de juntar comum com outras tabelas
- Se a adesão for menos significativa do que outras juntas na consulta
- Quando a mesa é uma mesa de preparação temporária

Os dados do tutorial [Load New York taxicab](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) dão um exemplo de carregamento de dados numa mesa de preparação de robin redondo.


## <a name="choosing-a-distribution-column"></a>Escolher uma coluna de distribuição
Uma tabela distribuída por hash tem uma coluna de distribuição que é a chave hash. Por exemplo, o seguinte código cria uma tabela distribuída por hash com ProductKey como coluna de distribuição.

```SQL
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

Escolher uma coluna de distribuição é uma decisão importante de design, uma vez que os valores desta coluna determinam como as linhas são distribuídas. A melhor escolha depende de vários fatores, e geralmente envolve compensações. No entanto, se não escolher a melhor coluna da primeira vez, pode utilizar a [TABELA CREATE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para recriar a tabela com uma coluna de distribuição diferente. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Escolha uma coluna de distribuição que não requeira atualizações
Não é possível atualizar uma coluna de distribuição a não ser que apague a linha e insira uma nova linha com os valores atualizados. Portanto, selecione uma coluna com valores estáticos. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Escolha uma coluna de distribuição com dados que distribuam uniformemente

Para um melhor desempenho, todas as distribuições devem ter aproximadamente o mesmo número de linhas. Quando uma ou mais distribuições têm um número desproporcionado de linhas, algumas distribuições terminam a sua parte de uma consulta paralela antes de outras. Uma vez que a consulta não pode ser completada até que todas as distribuições tenham terminado o processamento, cada consulta é tão rápida quanto a distribuição mais lenta.

- O enviesamento de dados significa que os dados não são distribuídos uniformemente pelas distribuições
- O processamento de distorções significa que algumas distribuições demoram mais tempo do que outras quando executam consultas paralelas. Isto pode acontecer quando os dados são distorcidos.
  
Para equilibrar o processamento paralelo, selecione uma coluna de distribuição que:

- **Tem muitos valores únicos.** A coluna pode ter alguns valores duplicados. No entanto, todas as linhas com o mesmo valor são atribuídas à mesma distribuição. Uma vez que existem 60 distribuições, a coluna deve ter pelo menos 60 valores únicos.  Normalmente, o número de valores únicos é muito maior.
- **Não tem NULLs, ou tem apenas alguns NULLs.** Para um exemplo extremo, se todos os valores da coluna forem NULOS, todas as linhas são atribuídas à mesma distribuição. Como resultado, o processamento de consultas é desviado para uma distribuição, e não beneficia do processamento paralelo. 
- **Não é uma coluna de data.** Todos os dados para a mesma data aterram na mesma distribuição. Se vários utilizadores estiverem todos a filtrar na mesma data, então apenas 1 das 60 distribuições fazem todo o trabalho de processamento. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Escolha uma coluna de distribuição que minimize o movimento de dados

Para obter as consultas de resultados corretos, as consultas podem mover dados de um nó computacional para outro. O movimento de dados geralmente acontece quando as consultas têm adenárias e agregações em tabelas distribuídas. Escolher uma coluna de distribuição que ajude a minimizar o movimento de dados é uma das estratégias mais importantes para otimizar o desempenho do seu pool SQL Synapse.

Para minimizar o movimento de dados, selecione uma coluna de distribuição que:

- É usado `JOIN` `GROUP BY`em, `OVER`, `HAVING` `DISTINCT`e cláusulas. Quando duas grandes tabelas de fatos têm juntas frequentes, o desempenho da consulta melhora quando distribui ambas as tabelas numa das colunas de adesão.  Quando uma mesa não for utilizada em juntas, considere distribuir a tabela `GROUP BY` numa coluna que esteja frequentemente na cláusula.
- Não *not* é `WHERE` usado em cláusulas. Isto poderia limitar a consulta para não funcionar em todas as distribuições. 
- *Não* é uma coluna de data. Onde as cláusulas filtram frequentemente por data.  Quando isto acontece, todo o processamento pode funcionar em apenas algumas distribuições.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>O que fazer quando nenhuma das colunas é uma boa coluna de distribuição

Se nenhuma das suas colunas tiver valores distintos suficientes para uma coluna de distribuição, pode criar uma nova coluna como um composto de um ou mais valores. Para evitar o movimento de dados durante a execução da consulta, utilize a coluna de distribuição composta como coluna de adesão em consultas.

Uma vez que você projeta uma tabela distribuída por hash, o próximo passo é carregar dados na tabela.  Para obter orientação de carregamento, consulte [a visão geral do carregamento](design-elt-data-loading.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Como dizer se a sua coluna de distribuição é uma boa escolha
Depois de os dados serem carregados numa tabela distribuída por hash, verifique se as linhas são distribuídas uniformemente pelas 60 distribuições. As linhas por distribuição podem variar até 10% sem um impacto notável no desempenho. 

### <a name="determine-if-the-table-has-data-skew"></a>Determine se a tabela tem dados distorcidos
Uma forma rápida de verificar se os dados são distorcidos é utilizar [o DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). O seguinte código SQL devolve o número de linhas de mesa que são armazenadas em cada uma das 60 distribuições. Para um desempenho equilibrado, as linhas da sua tabela distribuída devem ser distribuídas uniformemente por todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Para identificar quais as tabelas que têm mais de 10% de distorção de dados:

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

### <a name="check-query-plans-for-data-movement"></a>Verifique os planos de consulta para o movimento de dados
Uma boa coluna de distribuição permite que as juntas e agregações tenham o mínimo de movimento de dados. Isto afeta a forma como as juntas devem ser escritas. Para obter o mínimo de movimento de dados para uma adesão em duas tabelas distribuídas por hash, uma das colunas de união precisa ser a coluna de distribuição.  Quando duas tabelas distribuídas por hash se juntam a uma coluna de distribuição do mesmo tipo de dados, a adesão não requer movimento de dados. A adesão pode utilizar colunas adicionais sem incorrer no movimento de dados.

Para evitar o movimento de dados durante uma adesão:

- As tabelas envolvidas na adesão devem ser distribuídas hash numa **das** colunas participantes na adesão.
- Os tipos de dados das colunas de adesão devem coincidir entre ambas as tabelas.
- As colunas devem ser unidas a um operador igual.
- O tipo de adesão pode não ser um `CROSS JOIN`.

Para ver se as consultas estão experimentando o movimento de dados, você pode olhar para o plano de consulta.  


## <a name="resolve-a-distribution-column-problem"></a>Resolver um problema de coluna de distribuição
Não é necessário resolver todos os casos de distorção de dados. A distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizar o enviesamento de dados e o movimento de dados. Nem sempre é possível minimizar tanto o enviesamento de dados como o movimento de dados. Por vezes, o benefício de ter o movimento mínimo de dados pode superar o impacto de ter dados distorcidos.

Para decidir se deve resolver o enviesamento de dados numa tabela, deve compreender o máximo possível sobre os volumes de dados e consultas na sua carga de trabalho. Pode utilizar os passos do artigo de [monitorização](sql-data-warehouse-manage-monitor.md) da Consulta para monitorizar o impacto do enviesamento no desempenho da consulta. Especificamente, procure quanto tempo leva grandes consultas para completar em distribuições individuais.

Uma vez que não é possível alterar a coluna de distribuição numa tabela existente, a forma típica de resolver o enviesamento de dados é recriar a tabela com uma coluna de distribuição diferente.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Recriar a tabela com uma nova coluna de distribuição
Este exemplo utiliza a [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) para recriar uma tabela com uma coluna de distribuição de hash diferente.

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

- [TABELA DE CRIAÇÃO (piscina SYnapse SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CRIAR TABELA COMO SELECT (piscina Synapse SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


