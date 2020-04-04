---
title: Orientação de design para tabelas replicadas
description: Recomendações para conceber tabelas replicadas no Synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 128b4203d34b99df8363ef19783baa4a7b608aa5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631320"
---
# <a name="design-guidance-for-using-replicated-tables-in-sql-analytics"></a>Orientação de design para a utilização de tabelas replicadas no SQL Analytics

Este artigo dá recomendações para conceber tabelas replicadas no seu esquema SQL Analytics. Utilize estas recomendações para melhorar o desempenho da consulta, reduzindo o movimento de dados e a complexidade da consulta.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com os conceitos de distribuição de dados e movimento de dados no SQL Analytics.Para mais informações, consulte o artigo da [arquitetura.](massively-parallel-processing-mpp-architecture.md)

Como parte do design de tabela, compreenda o máximo possível sobre os seus dados e como os dados são consultados.Por exemplo, considere estas questões:

- Qual é o tamanho da mesa?
- Quantas vezes a mesa é refrescada?
- Tenho tabelas de factos e dimensões numa base de dados da SQL Analytics?

## <a name="what-is-a-replicated-table"></a>O que é uma mesa replicada?

Uma tabela replicada tem uma cópia completa da tabela acessível em cada nó computacional. A replicação de uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de se proceder a uma associação ou agregação. Uma vez que a tabela tem várias cópias, as tabelas replicadas funcionam melhor quando o tamanho da mesa é inferior a 2 GB comprimido.  2 GB não é um limite difícil.  Se os dados forem estáticos e não mudarem, pode replicar tabelas maiores.

O diagrama seguinte mostra uma tabela replicada que é acessível em cada nó computacional. No SQL Analytics, a tabela replicada é totalmente copiada para uma base de dados de distribuição em cada nó computacional.

![Tabela replicada](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabela replicada")  

As mesas replicadas funcionam bem para tabelas de dimensão num esquema estelar. As tabelas de dimensão são tipicamente unidas a tabelas de factos que são distribuídas de forma diferente da tabela de dimensões.  As dimensões são geralmente de um tamanho que torna viável armazenar e manter várias cópias. As dimensões armazenam dados descritivos que mudam lentamente, como o nome e endereço do cliente, e detalhes do produto. A natureza em constante mudança dos dados leva a uma menor manutenção da tabela replicada.

Considere usar uma tabela replicada quando:

- O tamanho da mesa no disco é inferior a 2 GB, independentemente do número de linhas. Para encontrar o tamanho de uma mesa, pode `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`utilizar o comando [dBCC PDW_SHOWSPACEUSED:](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .
- A tabela é utilizada em juntas que de outra forma exigiriam movimento de dados. Ao juntar tabelas que não são distribuídas na mesma coluna, como uma tabela distribuída por hash para uma mesa de robin redondo, é necessário um movimento de dados para completar a consulta.  Se uma das mesas for pequena, considere uma mesa replicada. Recomendamos a utilização de mesas replicadas em vez de mesas de rodapé na maioria dos casos. Para visualizar as operações de movimento de dados em planos de consulta, utilize [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  A BroadcastMoveOperation é a operação típica de movimento de dados que pode ser eliminada utilizando uma tabela replicada.  

As tabelas replicadas não podem produzir o melhor desempenho de consulta quando:

- A tabela tem operações frequentes de inserção, atualização e exclusão.As operações da linguagem de manipulação de dados (DML) requerem uma reconstrução da tabela replicada.A reconstrução frequentemente pode causar um desempenho mais lento.
- A base de dados SQL Analytics é dimensionada com frequência. A escala de uma base de dados SQL Analytics altera o número de nós computacionais, que incorrem na reconstrução da tabela replicada.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acedem apenas a um pequeno número de colunas. Neste cenário, em vez de replicar toda a tabela, pode ser mais eficaz distribuir a tabela e, em seguida, criar um índice nas colunas frequentemente acedidas. Quando uma consulta requer movimento de dados, o SQL Analytics apenas move dados para as colunas solicitadas.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Use tabelas replicadas com predicados de consulta simples

Antes de optar por distribuir ou replicar uma tabela, pense nos tipos de consultas que planeia fazer contra a mesa. Sempre que possível,

- Utilize tabelas replicadas para consultas com predicados de consulta simples, tais como igualdade ou desigualdade.
- Utilize tabelas distribuídas para consultas com predicados de consultas complexas, como LIKE ou NOT LIKE.

As consultas intensivas em CPU têm melhor desempenho quando o trabalho é distribuído por todos os nós da Compute. Por exemplo, as consultas que executam computações em cada linha de uma mesa têm um melhor desempenho em tabelas distribuídas do que em tabelas replicadas. Uma vez que uma mesa replicada é armazenada na íntegra em cada nó computacional, uma consulta intensiva de CPU contra uma mesa replicada corre contra toda a mesa em cada nó computacional. A computação extra pode atrasar o desempenho da consulta.

Por exemplo, esta consulta tem um predicado complexo.  Funciona mais rápido quando os dados estão numa tabela distribuída em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuídos em rodada.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter mesas redondas existentes em mesas replicadas

Se já tem mesas de rodapé, recomendamos que as converta em tabelas replicadas se cumprirem os critérios descritos neste artigo. As tabelas replicadas melhoram o desempenho em cima das tabelas de rodapé porque eliminam a necessidade de movimento de dados.  Uma mesa de robin redondo requer sempre movimento de dados para uniões.

Este exemplo utiliza [ctas](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para mudar a tabela DimSalesTerritory para uma tabela replicada. Este exemplo funciona independentemente de o DimSalesTerritory ser distribuído por haxixe ou arredondamento.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta para arredondado versus replicado

Uma tabela replicada não requer qualquer movimento de dados para uniões porque toda a tabela já está presente em cada nó computacional. Se as tabelas de dimensão forem distribuídas em rodada, uma junta copia a tabela de dimensões na íntegra para cada nó computacional. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Este tipo de operação de movimento de dados retarda o desempenho da consulta e é eliminado utilizando tabelas replicadas. Para ver os passos do plano de consulta, utilize a vista de catálogo do sistema [sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

Por exemplo, na seguinte consulta contra o esquema `FactInternetSales` AdventureWorks, a tabela é distribuída por hash. As `DimDate` `DimSalesTerritory` mesas e as mesas são tabelas de dimensão menor. Esta consulta devolve o total das vendas na América do Norte para o ano fiscal de 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Recriamos `DimDate` e `DimSalesTerritory` como mesas de rodapé. Como resultado, a consulta mostrou o seguinte plano de consulta, que tem múltiplas operações de movimento de transmissão:

![Plano de consulta de robin redondo](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Recriámos `DimDate` e `DimSalesTerritory` como mesas replicadas, e fizemos a consulta novamente. O plano de consulta resultante é muito mais curto e não tem quaisquer movimentos de transmissão.

![Plano de consulta replicado](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações de desempenho para modificar tabelas replicadas

A SQL Analytics implementa uma tabela replicada mantendo uma versão master da tabela. Copia a versão principal para uma base de dados de distribuição em cada nó computacional. Quando há uma mudança, a SQL Analytics atualiza primeiro a tabela principal. Depois reconstrói as mesas em cada nó computacional. Uma reconstrução de uma tabela replicada inclui copiar a tabela para cada nó computacional e, em seguida, construir os índices.  Por exemplo, uma tabela replicada num DW400 tem 5 cópias dos dados.  Uma cópia principal e uma cópia completa em cada nó computacional.  Todos os dados são armazenados em bases de dados de distribuição. O SQL Analytics utiliza este modelo para suportar declarações mais rápidas de modificação de dados e operações de escala flexível.

As reconstruções são necessárias após:

- Os dados são carregados ou modificados
- A instância SQL synapse é dimensionada para um nível diferente
- A definição de tabela é atualizada

As reconstruções não são necessárias após:

- Pausa operação
- Retomar operação

A reconstrução não acontece imediatamente após a modificação dos dados. Em vez disso, a reconstrução é desencadeada pela primeira vez que uma consulta seleciona da mesa.  A consulta que desencadeou a reconstrução lê-se imediatamente a partir da versão principal da tabela enquanto os dados são assincronicamente copiados para cada nó computacional. Até que a cópia de dados esteja completa, as consultas subsequentes continuarão a utilizar a versão principal da tabela.  Se alguma atividade ocorrer contra a tabela replicada que força outra reconstrução, a cópia de dados é invalidada e a próxima declaração selecionada irá desencadear dados para serem copiados novamente.

### <a name="use-indexes-conservatively"></a>Use índices de forma conservadora

As práticas de indexação padrão aplicam-se a tabelas replicadas. A SQL Analytics reconstrói cada índice de tabela replicado como parte da reconstrução. Utilize apenas índices quando o ganho de desempenho supera o custo de reconstrução dos índices.

### <a name="batch-data-load"></a>Carga de dados de lote

Ao carregar os dados em tabelas replicadas, tente minimizar as reconstruções, emlotando cargas em conjunto. Execute todas as cargas lotadas antes de executar as declarações selecionadas.

Por exemplo, este padrão de carga carrega dados de quatro fontes e invoca quatro reconstruções.

        Load from source 1.
- Selecione os gatilhos de declaração reconstruir 1.
        Carga da fonte 2.
- Selecione os gatilhos de declaração reconstruir 2.
- Carga da fonte 3.
- Selecione os gatilhos de declaração reconstruir 3.
- Carga da fonte 4.
- Selecione os gatilhos de declaração reconstruir 4.

Por exemplo, este padrão de carga carrega dados de quatro fontes, mas apenas invoca uma reconstrução.

- Carga da fonte 1.
- Carga da fonte 2.
- Carga da fonte 3.
- Carga da fonte 4.
- Selecione os gatilhos de declaração que se reconstroem.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Reconstruir uma mesa replicada após uma carga de lote

Para garantir tempos de execução de consulta consistentes, considere forçar a construção das tabelas replicadas após uma carga de lote. Caso contrário, a primeira consulta continuará a utilizar o movimento de dados para completar a consulta.

Esta consulta usa o [DMV sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para listar as tabelas replicadas que foram modificadas, mas não reconstruídas.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Para desencadear uma reconstrução, execute a seguinte declaração em cada tabela na saída anterior.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Passos seguintes

Para criar uma tabela replicada, use uma destas declarações:

- [TABELA CREATE (SQL Analytics)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA AS SELECT (SQL Analytics)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Para uma visão geral das tabelas distribuídas, consulte [as tabelas distribuídas](sql-data-warehouse-tables-distribute.md).
