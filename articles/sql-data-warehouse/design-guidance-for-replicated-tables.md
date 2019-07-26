---
title: Diretrizes de design para tabelas replicadas – Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações para criar tabelas replicadas no seu esquema de SQL Data Warehouse do Azure. 
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c622edc6c3a37b2bc71323cf0e2c155f7aec6e33
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479317"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Diretrizes de design para usar tabelas replicadas no Azure SQL Data Warehouse
Este artigo fornece recomendações para criar tabelas replicadas em seu esquema de SQL Data Warehouse. Use essas recomendações para melhorar o desempenho da consulta, reduzindo a movimentação de dados e a complexidade da consulta.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com os conceitos de distribuição de dados e movimentação de dados em SQL Data Warehouse.  Para obter mais informações, consulte o artigo [arquitetura](massively-parallel-processing-mpp-architecture.md) . 

Como parte do design da tabela, entenda o máximo possível sobre seus dados e como os dados são consultados.  Por exemplo, considere estas perguntas:

- Qual é o tamanho da tabela?   
- Com que frequência a tabela é atualizada?   
- Tenho tabelas de dimensões e de fatos em um data warehouse?   

## <a name="what-is-a-replicated-table"></a>O que é uma tabela replicada?
Uma tabela replicada tem uma cópia completa da tabela acessível em cada nó de computação. A replicação de uma tabela elimina a necessidade de transferir dados entre nós de computação antes de uma junção ou agregação. Como a tabela tem várias cópias, as tabelas replicadas funcionam melhor quando o tamanho da tabela é menor que 2 GB compactados.  2 GB não é um limite rígido.  Se os dados forem estáticos e não forem alterados, você poderá replicar tabelas maiores.

O diagrama a seguir mostra uma tabela replicada que pode ser acessada em cada nó de computação. No SQL Data Warehouse, a tabela replicada é totalmente copiada para um banco de dados de distribuição em cada nó de computação. 

![Tabela replicada](media/guidance-for-using-replicated-tables/replicated-table.png "Tabela replicada")  

As tabelas replicadas funcionam bem para tabelas de dimensões em um esquema em estrela. As tabelas de dimensões normalmente são unidas a tabelas de fatos que são distribuídas de maneira diferente da tabela de dimensões.  Normalmente, as dimensões são de um tamanho que torna viável armazenar e manter várias cópias. As dimensões armazenam dados descritivos que são alterados lentamente, como nome do cliente e endereço e detalhes do produto. A natureza de alteração lenta dos dados leva a menos manutenção da tabela replicada. 

Considere usar uma tabela replicada quando:

- O tamanho da tabela no disco é menor que 2 GB, independentemente do número de linhas. Para localizar o tamanho de uma tabela, você pode usar o comando [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) : `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tabela é usada em junções que, de outra forma, exigirão a movimentação de dados. Ao unir tabelas que não são distribuídas na mesma coluna, como uma tabela distribuída por hash a uma tabela Round Robin, a movimentação de dados é necessária para concluir a consulta.  Se uma das tabelas for pequena, considere uma tabela replicada. É recomendável usar tabelas replicadas em vez de tabelas Round Robin na maioria dos casos. Para exibir as operações de movimentação de dados em planos de consulta, use [Sys. dm _pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  O BroadcastMoveOperation é a operação de movimentação de dados típica que pode ser eliminada usando uma tabela replicada.  
 
Tabelas replicadas podem não produzir o melhor desempenho de consulta quando:

- A tabela tem operações frequentes de inserção, atualização e exclusão. Essas operações DML (linguagem de manipulação de dados) exigem uma recompilação da tabela replicada. A recompilação com frequência pode causar um desempenho mais lento.
- A data warehouse é dimensionada com frequência. Dimensionar um data warehouse altera o número de nós de computação, o que incorre na recriação da tabela replicada.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acessam apenas um pequeno número de colunas. Nesse cenário, em vez de replicar a tabela inteira, pode ser mais eficiente distribuir a tabela e, em seguida, criar um índice nas colunas acessadas com frequência. Quando uma consulta requer movimentação de dados, SQL Data Warehouse apenas move dados para as colunas solicitadas. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usar tabelas replicadas com predicados de consulta simples
Antes de escolher distribuir ou replicar uma tabela, pense nos tipos de consultas que você planeja executar na tabela. Sempre que possível,

- Use tabelas replicadas para consultas com predicados de consulta simples, como igualdade ou desigualdade.
- Use tabelas distribuídas para consultas com predicados de consulta complexos, como LIKE ou NOT LIKE.

As consultas com uso intensivo de CPU têm melhor desempenho quando o trabalho é distribuído em todos os nós de computação. Por exemplo, as consultas que executam cálculos em cada linha de uma tabela têm melhor desempenho em tabelas distribuídas do que as tabelas replicadas. Como uma tabela replicada é armazenada cheia em cada nó de computação, uma consulta com uso intensivo de CPU em uma tabela replicada é executada em toda a tabela em cada nó de computação. A computação extra pode reduzir o desempenho da consulta.

Por exemplo, essa consulta tem um predicado complexo.  Ele é executado mais rapidamente quando os dados estão em uma tabela distribuída em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuídos em rodízio.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter tabelas Round Robin existentes em tabelas replicadas
Se você já tiver tabelas Round Robin, é recomendável convertê-las em tabelas replicadas se elas atenderem aos critérios descritos neste artigo. As tabelas replicadas melhoram o desempenho em tabelas Round Robin porque eliminam a necessidade de movimentação de dados.  Uma tabela Round Robin sempre requer a movimentação de dados para junções. 

Este exemplo usa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para alterar a tabela DimSalesTerritory para uma tabela replicada. Este exemplo funciona independentemente se DimSalesTerritory é distribuído por hash ou Round Robin.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta para Round-Robin versus replicado 

Uma tabela replicada não requer nenhuma movimentação de dados para junções porque a tabela inteira já está presente em cada nó de computação. Se as tabelas de dimensões forem distribuídas em rodízio, uma junção copiará a tabela de dimensões Full para cada nó de computação. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Esse tipo de operação de movimentação de dados reduz o desempenho da consulta e é eliminada usando tabelas replicadas. Para exibir as etapas do plano de consulta, use a exibição do catálogo do sistema [Sys. dm _pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) . 

Por exemplo, na consulta a seguir em relação ao esquema AdventureWorks `FactInternetSales` , a tabela é distribuída por hash. As `DimDate` tabelas `DimSalesTerritory` e são tabelas de dimensões menores. Essa consulta retorna o total de vendas em América do Norte do ano fiscal 2004:

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
Recriamos `DimDate` e `DimSalesTerritory` como tabelas Round Robin. Como resultado, a consulta mostrou o plano de consulta a seguir, que tem várias operações de movimentação de difusão: 
 
![Plano de consulta Round Robin](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Recriamos `DimDate` e `DimSalesTerritory` como tabelas replicadas e executamos a consulta novamente. O plano de consulta resultante é muito mais curto e não tem nenhuma movimentação de difusão.

![Plano de consulta replicado](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações de desempenho para modificar tabelas replicadas
SQL Data Warehouse implementa uma tabela replicada mantendo uma versão mestra da tabela. Ele copia a versão mestra para um banco de dados de distribuição em cada nó de computação. Quando há uma alteração, SQL Data Warehouse primeiro atualiza a tabela mestra. Em seguida, ele recria as tabelas em cada nó de computação. Uma recompilação de uma tabela replicada inclui copiar a tabela para cada nó de computação e, em seguida, criar os índices.  Por exemplo, uma tabela replicada em um DW400 tem 5 cópias dos dados.  Uma cópia mestra e uma cópia completa em cada nó de computação.  Todos os dados são armazenados em bancos de dado de distribuição. SQL Data Warehouse usa esse modelo para dar suporte a instruções de modificação de dados mais rápidas e operações de dimensionamento flexíveis. 

As recompilações são necessárias após:
- Os dados são carregados ou modificados
- A data warehouse é dimensionada para um nível diferente
- A definição da tabela está atualizada

As recompilações não são necessárias após:
- Pausar operação
- Retomar operação

A recompilação não acontece imediatamente depois que os dados são modificados. Em vez disso, a recompilação é disparada na primeira vez que uma consulta é selecionada na tabela.  A consulta que disparou a recriação lê imediatamente a partir da versão mestre da tabela, enquanto os dados são copiados de forma assíncrona para cada nó de computação. Até que a cópia de dados seja concluída, as consultas subsequentes continuarão a usar a versão mestra da tabela.  Se alguma atividade ocorrer em relação à tabela replicada que força outra recompilação, a cópia de dados será invalidada e a próxima instrução SELECT irá disparar os dados a serem copiados novamente. 

### <a name="use-indexes-conservatively"></a>Usar índices de forma conservadora
As práticas de indexação padrão se aplicam a tabelas replicadas. SQL Data Warehouse recria cada índice de tabela replicado como parte da recompilação. Use índices somente quando o lucro de desempenho aumentar o custo da recriação dos índices.  
 
### <a name="batch-data-loads"></a>Cargas de dados do lote
Ao carregar dados em tabelas replicadas, tente minimizar as recompilações enviando cargas em lote. Execute todas as cargas em lote antes de executar as instruções SELECT.

Por exemplo, esse padrão de carga carrega dados de quatro fontes e invoca quatro recompilações. 

- Carregar da origem 1.
- A instrução SELECT dispara a recompilação 1.
- Carregar da origem 2.
- A instrução SELECT dispara a recompilação 2.
- Carregar da origem 3.
- A instrução SELECT dispara a recompilação 3.
- Carregar da origem 4.
- A instrução SELECT dispara a recompilação 4.

Por exemplo, esse padrão de carga carrega dados de quatro fontes, mas só invoca uma recompilação.

- Carregar da origem 1.
- Carregar da origem 2.
- Carregar da origem 3.
- Carregar da origem 4.
- A instrução SELECT aciona a recompilação.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Recompilar uma tabela replicada após um carregamento em lote
Para garantir tempos de execução de consulta consistentes, considere forçar a compilação das tabelas replicadas após um carregamento em lote. Caso contrário, a primeira consulta ainda usará a movimentação de dados para concluir a consulta. 

Essa consulta usa a DMV [Sys. PDW _replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) para listar as tabelas replicadas que foram modificadas, mas não recriadas.

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
 
Para disparar uma recompilação, execute a seguinte instrução em cada tabela na saída anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Passos Seguintes 
Para criar uma tabela replicada, use uma destas instruções:

- [CREATE TABLE (SQL Data Warehouse do Azure)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE como SELECT (SQL Data Warehouse do Azure)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Para obter uma visão geral das tabelas distribuídas, consulte [tabelas distribuídas](sql-data-warehouse-tables-distribute.md).



