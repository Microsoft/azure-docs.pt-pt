---
title: Diretrizes de design de tabelas distribuídas-Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações para a criação de tabelas distribuídas por hash e de Round Robin no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4b322415592a7202387cb6776d2c040cda765b27
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479358"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Diretrizes para criar tabelas distribuídas no Azure SQL Data Warehouse
Recomendações para a criação de tabelas distribuídas por hash e de Round Robin no Azure SQL Data Warehouse.

Este artigo pressupõe que você esteja familiarizado com os conceitos de distribuição de dados e movimentação de dados em SQL Data Warehouse.  Para obter mais informações, consulte [arquitetura de processamento paralelo maciço (MPP) do Azure SQL data warehouse](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>O que é uma tabela distribuída?
Uma tabela distribuída aparece como uma única tabela, mas as linhas são realmente armazenadas entre 60 distribuições. As linhas são distribuídas com um algoritmo de hash ou Round Robin.  

As **tabelas distribuídas por hash** melhoram o desempenho da consulta em grandes tabelas de fatos e são o foco deste artigo. As **tabelas de Round Robin** são úteis para melhorar a velocidade de carregamento. Essas opções de design têm um impacto significativo no aprimoramento do desempenho da consulta e do carregamento.

Outra opção de armazenamento de tabela é replicar uma pequena tabela em todos os nós de computação. Para obter mais informações, consulte [diretrizes de design para tabelas replicadas](design-guidance-for-replicated-tables.md). Para escolher rapidamente entre as três opções, consulte tabelas distribuídas na [visão geral de tabelas](sql-data-warehouse-tables-overview.md). 

Como parte do design da tabela, entenda o máximo possível sobre seus dados e como os dados são consultados.  Por exemplo, considere estas perguntas:

- Qual é o tamanho da tabela?   
- Com que frequência a tabela é atualizada?   
- Tenho tabelas de dimensões e de fatos em um data warehouse?   


### <a name="hash-distributed"></a>Hash distribuído
Uma tabela distribuída por hash distribui linhas de tabela nos nós de computação usando uma função de hash determinística para atribuir cada linha a uma [distribuição](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela distribuída")  

Como valores idênticos sempre são hash para a mesma distribuição, o data warehouse tem conhecimento interno dos locais de linha. SQL Data Warehouse usa esse conhecimento para minimizar a movimentação de dados durante consultas, o que melhora o desempenho da consulta. 

As tabelas distribuídas por hash funcionam bem para grandes tabelas de fatos em um esquema em estrela. Eles podem ter um número muito grande de linhas e ainda obter alto desempenho. Há, é claro, algumas considerações de design que ajudam você a obter o desempenho que o sistema distribuído foi projetado para fornecer. Escolher uma boa coluna de distribuição é uma das considerações descritas neste artigo. 

Considere usar uma tabela distribuída por hash quando:

- O tamanho da tabela em disco é maior que 2 GB.
- A tabela tem operações frequentes de inserção, atualização e exclusão. 

### <a name="round-robin-distributed"></a>Round Robin distribuído
Uma tabela distribuída por Round Robin distribui linhas de tabela uniformemente em todas as distribuições. A atribuição de linhas a distribuições é aleatória. Diferentemente de tabelas distribuídas por hash, não há garantia de que as linhas com valores iguais sejam atribuídas à mesma distribuição. 

Como resultado, o sistema às vezes precisa invocar uma operação de movimentação de dados para organizar melhor seus dados antes de poder resolver uma consulta.  Essa etapa extra pode retardar suas consultas. Por exemplo, ingressar em uma tabela Round Robin geralmente requer embaralhando as linhas, o que é um impacto no desempenho.

Considere usar a distribuição Round Robin para sua tabela nos seguintes cenários:

- Ao começar como um ponto de partida simples, pois ele é o padrão
- Se não houver nenhuma chave de junção óbvia
- Se não houver boa coluna candidata para a distribuição de hash da tabela
- Se a tabela não compartilhar uma chave de junção comum com outras tabelas
- Se a junção for menos significativa do que outras junções na consulta
- Quando a tabela é uma tabela de preparo temporária

O tutorial [carregar dados de dos táxis de Nova York no Azure SQL data warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) fornece um exemplo de carregamento de dados em uma tabela de preparo de Round-Robin.


## <a name="choosing-a-distribution-column"></a>Escolhendo uma coluna de distribuição
Uma tabela distribuída por hash tem uma coluna de distribuição que é a chave de hash. Por exemplo, o código a seguir cria uma tabela distribuída por hash com ProductKey como a coluna de distribuição.

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

Escolher uma coluna de distribuição é uma decisão de design importante, pois os valores nesta coluna determinam como as linhas são distribuídas. A melhor opção depende de vários fatores e geralmente envolve as compensações. No entanto, se você não escolher a melhor coluna na primeira vez, poderá usar [CREATE TABLE como SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para recriar a tabela com uma coluna de distribuição diferente. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Escolha uma coluna de distribuição que não exija atualizações
Não é possível atualizar uma coluna de distribuição a menos que você exclua a linha e insira uma nova linha com os valores atualizados. Portanto, selecione uma coluna com valores estáticos. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Escolha uma coluna de distribuição com dados que distribui uniformemente

Para obter o melhor desempenho, todas as distribuições devem ter aproximadamente o mesmo número de linhas. Quando uma ou mais distribuições têm um número desproporcional de linhas, algumas distribuições terminam sua parte de uma consulta paralela antes de outras. Como a consulta não pode ser concluída até que todas as distribuições tenham terminado o processamento, cada consulta é tão rápida quanto a distribuição mais lenta.

- Distorção de dados significa que os dados não são distribuídos uniformemente entre as distribuições
- O processamento de distorção significa que algumas distribuições demoram mais do que outras ao executar consultas paralelas. Isso pode acontecer quando os dados estão distorcidos.
  
Para balancear o processamento paralelo, selecione uma coluna de distribuição que:

- **Tem muitos valores exclusivos.** A coluna pode ter alguns valores duplicados. No entanto, todas as linhas com o mesmo valor são atribuídas à mesma distribuição. Como há 60 distribuições, a coluna deve ter pelo menos 60 valores exclusivos.  Geralmente, o número de valores exclusivos é muito maior.
- **Não tem valores nulos ou tem apenas alguns valores nulos.** Para um exemplo extremo, se todos os valores na coluna forem nulos, todas as linhas serão atribuídas à mesma distribuição. Como resultado, o processamento de consultas é distorcido para uma distribuição e não se beneficia do processamento paralelo. 
- **Não é uma coluna de data**. Todos os dados da mesma data ficam na mesma distribuição. Se vários usuários estiverem todos filtrando na mesma data, somente uma das distribuições 60 fará todo o trabalho de processamento. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Escolha uma coluna de distribuição que minimize a movimentação de dados

Para obter as consultas corretas do resultado da consulta, é possível mover dados de um nó de computação para outro. A movimentação de dados normalmente ocorre quando as consultas têm junções e agregações em tabelas distribuídas. Escolher uma coluna de distribuição que ajude a minimizar a movimentação de dados é uma das estratégias mais importantes para otimizar o desempenho do seu SQL Data Warehouse.

Para minimizar a movimentação de dados, selecione uma coluna de distribuição que:

- É usado nas `JOIN`cláusulas `DISTINCT`, `GROUP BY`, `OVER`, `HAVING` e. Quando duas grandes tabelas de fatos têm junções frequentes, o desempenho da consulta melhora quando você distribui ambas as tabelas em uma das colunas de junção.  Quando uma tabela não é usada em junções, considere distribuir a tabela em uma coluna com frequência na `GROUP BY` cláusula.
- *Não* é usado em `WHERE` cláusulas. Isso poderia restringir a consulta para não ser executada em todas as distribuições. 
- *Não* é uma coluna de data. As cláusulas WHERE geralmente filtram por data.  Quando isso acontece, todo o processamento podia ser executado em apenas algumas distribuições.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>O que fazer quando nenhuma das colunas for uma boa coluna de distribuição

Se nenhuma das suas colunas tiver valores distintos suficientes para uma coluna de distribuição, você poderá criar uma nova coluna como uma composição de um ou mais valores. Para evitar a movimentação de dados durante a execução da consulta, use a coluna de distribuição composta como uma coluna de junção em consultas.

Depois de criar uma tabela distribuída por hash, a próxima etapa é carregar dados na tabela.  Para obter diretrizes de carregamento, consulte [carregando visão geral](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Como saber se a coluna de distribuição é uma boa opção
Depois que os dados são carregados em uma tabela distribuída por hash, verifique para ver como as linhas são distribuídas uniformemente entre as distribuições 60. As linhas por distribuição podem variar até 10% sem um impacto perceptível no desempenho. 

### <a name="determine-if-the-table-has-data-skew"></a>Determinar se a tabela tem distorção de dados
Uma maneira rápida de verificar a distorção de dados é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). O código SQL a seguir retorna o número de linhas de tabela que são armazenadas em cada uma das distribuições 60. Para o desempenho equilibrado, as linhas em sua tabela distribuída devem ser distribuídas uniformemente entre todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Para identificar quais tabelas têm mais de 10% de distorção de dados:

1. Crie o modo de exibição dbo. vTableSizes que é mostrado no artigo [visão geral de tabelas](sql-data-warehouse-tables-overview.md#table-size-queries) .  
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

### <a name="check-query-plans-for-data-movement"></a>Verificar planos de consulta para movimentação de dados
Uma boa coluna de distribuição permite que junções e agregações tenham uma movimentação mínima de dados. Isso afeta a maneira como as junções devem ser gravadas. Para obter uma movimentação mínima de dados para uma junção em duas tabelas distribuídas por hash, uma das colunas de junção precisa ser a coluna de distribuição.  Quando duas tabelas distribuídas por hash ingressam em uma coluna de distribuição do mesmo tipo de dados, a junção não requer a movimentação de dados. As junções podem usar colunas adicionais sem incorrer em movimento de dados.

Para evitar a movimentação de dados durante uma junção:

- As tabelas envolvidas na junção devem ser distribuídas por hash em **uma** das colunas que participam da junção.
- Os tipos de dados das colunas de junção devem corresponder entre ambas as tabelas.
- As colunas devem ser unidas com um operador Equals.
- O tipo de junção pode não ser `CROSS JOIN`um.

Para ver se as consultas estão passando por movimentação de dados, você pode examinar o plano de consulta.  


## <a name="resolve-a-distribution-column-problem"></a>Resolver um problema de coluna de distribuição
Não é necessário resolver todos os casos de distorção de dados. A distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizar a distorção de dados e a movimentação de dados. Nem sempre é possível minimizar a distorção de dados e a movimentação de dados. Às vezes, o benefício de ter a movimentação mínima de dados pode superar o impacto da distorção de dados.

Para decidir se você deve resolver a distorção de dados em uma tabela, você deve entender o máximo possível sobre os volumes de dados e consultas em sua carga de trabalho. Você pode usar as etapas no artigo [monitoramento de consultas](sql-data-warehouse-manage-monitor.md) para monitorar o impacto da distorção no desempenho da consulta. Especificamente, procure por quanto tempo as consultas grandes são concluídas em distribuições individuais.

Como você não pode alterar a coluna de distribuição em uma tabela existente, a maneira típica de resolver a distorção de dados é recriar a tabela com uma coluna de distribuição diferente.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Recriar a tabela com uma nova coluna de distribuição
Este exemplo usa [CREATE TABLE como SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) para recriar uma tabela com uma coluna de distribuição de hash diferente.

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

## <a name="next-steps"></a>Passos Seguintes

Para criar uma tabela distribuída, use uma destas instruções:

- [CREATE TABLE (SQL Data Warehouse do Azure)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE como SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


