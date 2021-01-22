---
title: Orientação de design para tabelas replicadas
description: Recomendações para a conceção de mesas replicadas na piscina Sinaapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7dcb884d8eafdfa5218e96d63f62a5d462d20cf8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679935"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>Orientação de design para usar mesas replicadas na piscina Sinaapse SQL

Este artigo dá recomendações para a conceção de tabelas replicadas no seu esquema de piscina Synapse SQL. Utilize estas recomendações para melhorar o desempenho da consulta, reduzindo o movimento de dados e a complexidade da consulta.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com os conceitos de distribuição de dados e movimento de dados no pool SQL.  Para mais informações, consulte o artigo [arquitetura.](massively-parallel-processing-mpp-architecture.md)

Como parte do design de mesa, compreenda o máximo possível sobre os seus dados e como os dados são consultados.  Por exemplo, considere estas questões:

- Qual é o tamanho da mesa?
- Com que frequência a mesa é refrescada?
- Tenho tabelas de fatos e dimensões numa piscina SQL?

## <a name="what-is-a-replicated-table"></a>O que é uma mesa replicada?

Uma tabela replicada tem uma cópia completa da tabela acessível em cada nó computacional. A replicação de uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de se proceder a uma associação ou agregação. Uma vez que a tabela tem várias cópias, as mesas replicadas funcionam melhor quando o tamanho da mesa é inferior a 2 GB comprimido.  2 GB não é um limite difícil.  Se os dados forem estáticos e não mudarem, pode replicar tabelas maiores.

O diagrama seguinte mostra uma tabela replicada que está acessível em cada nó computacional. Na piscina SQL, a tabela replicada é totalmente copiada para uma base de dados de distribuição em cada nó de cálculo.

![Tabela replicada](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabela replicada")  

As mesas replicadas funcionam bem para as tabelas de dimensão num esquema estelar. As tabelas de dimensão são tipicamente unidas a tabelas de factos que são distribuídas de forma diferente da tabela de dimensões.  As dimensões são geralmente de um tamanho que torna possível armazenar e manter várias cópias. As dimensões armazenam dados descritivos que mudam lentamente, como o nome do cliente e endereço, e detalhes do produto. A mudança lenta da natureza dos dados leva a uma menor manutenção da tabela replicada.

Considere usar uma tabela replicada quando:

- O tamanho da mesa no disco é inferior a 2 GB, independentemente do número de linhas. Para encontrar o tamanho de uma mesa, pode utilizar o comando [de PDW_SHOWSPACEUSED DBCC:](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` .
- A tabela é usada em juntas que de outra forma exigiriam movimento de dados. Ao juntar mesas que não são distribuídas na mesma coluna, como uma mesa distribuída por haxixe para uma mesa de rodapé redondo, é necessário um movimento de dados para completar a consulta.  Se uma das mesas for pequena, considere uma mesa replicada. Recomendamos a utilização de mesas replicadas em vez de mesas de rodapé na maioria dos casos. Para visualizar as operações de movimento de dados nos planos de consulta, utilize [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  A BroadcastMoveOperation é a operação típica de movimento de dados que pode ser eliminada usando uma tabela replicada.  

As tabelas replicadas podem não produzir o melhor desempenho de consulta quando:

- A tabela tem operações frequentes de inserção, atualização e eliminação. As operações de linguagem de manipulação de dados (DML) requerem uma reconstrução da tabela replicada. A reconstrução frequentemente pode causar um desempenho mais lento.
- A piscina SQL é dimensionada com frequência. Escalar uma piscina SQL altera o número de nós Compute, que incorrem na reconstrução da tabela replicada.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acedem apenas a um pequeno número de colunas. Neste cenário, em vez de replicar toda a tabela, pode ser mais eficaz distribuir a tabela e, em seguida, criar um índice nas colunas frequentemente acedidas. Quando uma consulta requer movimento de dados, o pool SQL apenas move dados para as colunas solicitadas.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Use tabelas replicadas com predicados de consulta simples

Antes de optar por distribuir ou replicar uma tabela, pense nos tipos de consultas que pretende correr contra a mesa. Sempre que possível,

- Utilize tabelas replicadas para consultas com predicados simples, como igualdade ou desigualdade.
- Utilize tabelas distribuídas para consultas com predicados de consulta complexas, tais como LIKE ou NÃO LIKE.

As consultas intensivas de CPU têm um melhor desempenho quando o trabalho é distribuído por todos os nós computatários. Por exemplo, as consultas que executam computações em cada linha de uma mesa têm melhor desempenho em tabelas distribuídas do que em tabelas replicadas. Uma vez que uma tabela replicada é armazenada na totalidade em cada nó computo, uma consulta intensiva do CPU contra uma tabela replicada corre contra toda a tabela em cada nó compute. A computação extra pode retardar o desempenho da consulta.

Por exemplo, esta consulta tem um predicado complexo.  É mais rápido quando os dados estão numa tabela distribuída em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuídos por rodada.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter as tabelas de rodapé existentes em mesas replicadas

Se já tem mesas de rodapé, recomendamos convertê-las em mesas replicadas se cumprirem os critérios descritos neste artigo. As tabelas replicadas melhoram o desempenho em relação às tabelas de robin redondo porque eliminam a necessidade de movimento de dados.  Uma mesa de rodapé sempre requer movimento de dados para junções.

Este exemplo utiliza [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para alterar a tabela DimSalesTerritory para uma tabela replicada. Este exemplo funciona independentemente de o DimSalesTerritory ser distribuído por haxixe ou roda-robin.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta para rodada-robin versus replicado

Uma tabela replicada não requer qualquer movimento de dados para junções porque toda a tabela já está presente em cada nó computacional. Se as tabelas de dimensão forem distribuídas por rodapé, uma junção copia a tabela de dimensão na íntegra a cada nó computacional. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Este tipo de operação de movimento de dados retarda o desempenho da consulta e é eliminado usando tabelas replicadas. Para ver os passos do plano de consulta, utilize a vista do catálogo do sistema [sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

Por exemplo, ao seguir uma consulta contra o esquema adventureWorks, a `FactInternetSales` tabela é distribuída por haxixe. As `DimDate` `DimSalesTerritory` tabelas são tabelas de dimensão menor. Esta consulta devolve o total das vendas na América do Norte para o exercício de 2004:

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

Recriamos `DimDate` e `DimSalesTerritory` como mesas replicadas, e fizemos a consulta novamente. O plano de consulta resultante é muito mais curto e não tem quaisquer movimentos de transmissão.

![Plano de consulta replicado](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações de desempenho para modificar tabelas replicadas

A piscina SQL implementa uma tabela replicada mantendo uma versão principal da tabela. Copia a versão principal para a primeira base de dados de distribuição em cada nó Compute. Quando há uma mudança, a versão principal é atualizada primeiro, em seguida, as tabelas em cada nó computacional são reconstruídas. Uma reconstrução de uma tabela replicada inclui copiar a tabela para cada nó compute e, em seguida, construir os índices.  Por exemplo, uma tabela replicada num DW2000c tem 5 cópias dos dados.  Uma cópia principal e uma cópia completa em cada nó compute.  Todos os dados são armazenados em bases de dados de distribuição. O pool SQL utiliza este modelo para suportar declarações de modificação de dados mais rápidas e operações de escala flexíveis.

As reconstruções assíncronos são desencadeadas pela primeira consulta contra a tabela replicada após:

- Os dados são carregados ou modificados
- O exemplo Sinapse SQL é escalado para um nível diferente
- A definição de tabela é atualizada

As reconstruções não são necessárias após:

- Operação de pausa
- Retomar a operação

A reconstrução não acontece imediatamente após a modificação dos dados. Em vez disso, a reconstrução é desencadeada da primeira vez que uma consulta é selecionada a partir da tabela.  A consulta que desencadeou a reconstrução lê-se imediatamente a partir da versão principal da tabela enquanto os dados são assíncronosamente copiados para cada nó computo. Até que a cópia de dados esteja completa, as consultas subsequentes continuarão a utilizar a versão principal da tabela.  Se alguma atividade acontecer contra a tabela replicada que força outra reconstrução, a cópia de dados é invalidada e a próxima declaração selecionada irá desencadear dados para serem copiados novamente.

### <a name="use-indexes-conservatively"></a>Use índices de forma conservadora

As práticas de indexação padrão aplicam-se às tabelas replicadas. A piscina SQL reconstrói cada índice de tabela replicado como parte da reconstrução. Só utilize índices quando o ganho de desempenho supera o custo de reconstrução dos índices.

### <a name="batch-data-load"></a>Carga de dados de lote

Ao carregar dados em tabelas replicadas, tente minimizar as reconstruções, emgrutando cargas. Execute todas as cargas em lote antes de executar declarações selecionadas.

Por exemplo, este padrão de carga carrega dados de quatro fontes e invoca quatro reconstruções.

- Carga da fonte 1.
- Selecione os gatilhos de declaração reconstruem 1.
- Carga da fonte 2.
- Selecione os gatilhos de declaração para reconstruir 2.
- Carga da fonte 3.
- Selecione os gatilhos de declaração reconstruem 3.
- Carga da fonte 4.
- Selecione os gatilhos de declaração reconstruem 4.

Por exemplo, este padrão de carga carrega dados de quatro fontes, mas apenas invoca uma reconstrução.

- Carga da fonte 1.
- Carga da fonte 2.
- Carga da fonte 3.
- Carga da fonte 4.
- Selecione a demonstração de gatilhos de reconstrução.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Reconstruir uma tabela replicada após uma carga de lote

Para garantir tempos de execução de consultas consistentes, considere forçar a construção das tabelas replicadas após uma carga de lote. Caso contrário, a primeira consulta continuará a utilizar o movimento de dados para completar a consulta.

Esta consulta utiliza o DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para listar as tabelas replicadas que foram modificadas, mas não reconstruídas.

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

Para desencadear uma reconstrução, execute a seguinte declaração em cada mesa na saída anterior.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Passos seguintes

Para criar uma tabela replicada, utilize uma destas declarações:

- [CRIAR MESA (piscina SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CRIAR TABELA COMO SELECT (PISCINA SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Para uma visão geral das tabelas distribuídas, consulte [as tabelas distribuídas.](sql-data-warehouse-tables-distribute.md)
