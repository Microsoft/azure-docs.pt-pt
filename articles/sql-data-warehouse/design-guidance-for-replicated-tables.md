---
title: Documentação de orientação para as tabelas replicadas - Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações para estruturar replicados tabelas no seu esquema do Azure SQL Data Warehouse. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 93e1904862af7eaad395bf14b0d09555d9d1d2ab
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990140"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Documentação de orientação para uso tabelas replicadas no Azure SQL Data Warehouse
Este artigo fornece recomendações para a criação de tabelas replicadas no seu esquema do SQL Data Warehouse. Utilize estas recomendações para melhorar o desempenho de consulta, reduzindo a complexidade de movimento e a consulta de dados.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que esteja familiarizado com a distribuição de dados e conceitos de movimento de dados no SQL Data Warehouse.  Para obter mais informações, consulte a [arquitetura](massively-parallel-processing-mpp-architecture.md) artigo. 

Como parte do design da tabela, compreenda o máximo possível sobre os seus dados e como os dados são consultados.  Por exemplo, considere estas perguntas:

- Como grande, é a tabela?   
- Com que frequência é atualizada a tabela?   
- Tenho de tabelas de fatos e dimensão num data warehouse?   

## <a name="what-is-a-replicated-table"></a>O que é uma tabela replicada?
Tabelas replicadas tem uma cópia completa da tabela acessível em cada nó de computação. Replicar uma tabela remove a necessidade de transferir dados entre nós de computação antes de uma associação ou agregação. Uma vez que a tabela tem várias cópias, as tabelas replicadas funcionam melhor quando o tamanho da tabela é inferior a 2 GB comprimido.

O diagrama seguinte mostra uma tabela replicada que seja acessível em cada nó de computação. No SQL Data Warehouse, a tabela replicada totalmente é copiada para uma base de dados de distribuição em cada nó de computação. 

![Tabela de replicado](media/guidance-for-using-replicated-tables/replicated-table.png "replicado tabela")  

Replicados tabelas de trabalho bem para tabelas de pequenas dimensões num esquema em estrela. Tabelas de dimensões são, normalmente, um tamanho que o torna viável para armazenar e manter várias cópias. Dimensões armazenam descritivos dados alterados lentamente, por exemplo, o nome do cliente e o endereço e detalhes do produto. A natureza de mudança lenta dos dados acarreta menos recompilações da tabela replicada. 

Considere a utilização de um replicados de tabela quando:

- O tamanho da tabela no disco é inferior a 2 GB, independentemente do número de linhas. Para descobrir o tamanho de uma tabela, pode utilizar o [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) comando: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tabela é usada em junções exigiriam, caso contrário, movimento de dados. Ao associar as tabelas que não são distribuídas na mesma coluna, como uma tabela distribuída por hash a uma tabela round robin, movimento de dados é necessário para concluir a consulta.  Se uma das tabelas for pequena, considere uma tabela replicada. Recomendamos que utilize as tabelas replicadas em vez de tabelas round robin, na maioria dos casos. Para ver as operações de movimento de dados em planos de consulta, utilize [pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  O BroadcastMoveOperation é a operação de movimento de dados típica que pode ser eliminada utilizando tabelas replicadas.  
  Tabelas replicadas não poderão resultar em melhor desempenho de consulta quando:

- A tabela tem insert frequente, atualizar e eliminar operações. Estas operações de (DML linguagem) de manipulação de dados requerem uma reconstrução da tabela replicada. Reconstruir com frequência pode provocar um desempenho mais lento.
- O armazém de dados é dimensionado com frequência. Dimensionar um armazém de dados é alterado o número de nós de computação, o que implica uma reconstrução.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acederem apenas um pequeno número de colunas. Neste cenário, em vez de replicar a tabela inteira, poderá ser mais eficiente distribuir a tabela e, em seguida, criar um índice nas colunas acedidas com frequência. Quando uma consulta necessita de movimento de dados, o SQL Data Warehouse move apenas os dados para as colunas pedidas. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Utilizar tabelas replicadas com predicados de consulta simples
Antes de optar por distribuir ou replicar uma tabela, pense sobre os tipos de consultas que pretende executar com base na tabela. Sempre que possível,

- Utilize tabelas replicadas para consultas com predicados de consulta simples, como igualdade ou desigualdade.
- Utilizar tabelas distribuídas para consultas com predicados de consulta complexas, como LIKE ou não, como.

Consultas de intensiva da CPU têm um melhor desempenho quando o trabalho é distribuído por todos os nós de computação. Por exemplo, consultas que executam cálculos em cada linha de uma tabela funcionar melhor em tabelas distribuídas a tabelas replicadas. Como uma tabela replicada é armazenada por completo em cada nó de computação, executa uma consulta de intensiva da CPU em tabelas replicadas contra a tabela inteira em cada nó de computação. A computação extra pode abrandar o desempenho de consulta.

Por exemplo, esta consulta tem um predicado complexo.  Ele é executado mais rapidamente quando os dados estão numa tabela distribuída, em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuída round robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter tabelas de round robin existentes em tabelas replicadas
Se já tiver as tabelas round-robin, recomendamos que convertê-los para tabelas replicadas, se cumprirem os critérios descritos neste artigo. Tabelas replicadas melhorar o desempenho em tabelas round robin porque eliminam a necessidade de movimento de dados.  Uma tabela round-robin requer sempre movimento de dados de associações. 

Este exemplo utiliza [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para alterar a tabela DimSalesTerritory para tabelas replicadas. Este exemplo funciona independentemente DimSalesTerritory é distribuída por hash ou round robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta para round-robin versus replicados 

Tabelas replicadas não requerem qualquer movimento de dados para associações de porque a tabela inteira já está presente em cada nó de computação. Se as tabelas de dimensões são distribuída round robin, uma associação copia a tabela de dimensão completa para cada nó de computação. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Esse tipo de operação de movimento de dados abrandar o desempenho da consulta e é eliminado usando tabelas replicadas. Para visualizar as etapas do plano de consulta, utilize o [pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) vista de catálogo do sistema. 

Por exemplo, na seguinte consulta no esquema de AdventureWorks, o ` FactInternetSales` tabela é distribuída por hash. O `DimDate` e `DimSalesTerritory` as tabelas são tabelas de dimensão mais pequenas. Esta consulta devolve o total de vendas na América do Norte para o ano fiscal de 2004:
 
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
Vamos voltar a criar `DimDate` e `DimSalesTerritory` como tabelas round robin. Como resultado, a consulta mostrou que o plano de consulta seguinte, que tem vários difusão move operações: 
 
![Plano de consulta de round robin](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Vamos voltar a criar `DimDate` e `DimSalesTerritory` como tabelas replicadas e foi executada a consulta novamente. O plano de consulta resultante é muito menor e faz não tem qualquer difusão move.

![Plano de consulta de replicados](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações de desempenho para modificar tabelas replicadas
O SQL Data Warehouse implementa tabelas replicadas, ao manter uma versão principal da tabela. Copia a versão principal para uma base de dados de distribuição em cada nó de computação. Quando existe uma alteração, o SQL Data Warehouse primeiro atualiza a tabela mestra. Em seguida, ela recria as tabelas em cada nó de computação. Uma reconstrução de uma tabela replicada inclui copiar a tabela para cada nó de computação e, em seguida, criar os índices.  Por exemplo, uma tabela replicada num DW400 tem 5 cópias dos dados.  Uma cópia principal e uma cópia completa em cada nó de computação.  Todos os dados são armazenados nas bases de dados de distribuição. SQL Data Warehouse utiliza este modelo para suportar as instruções de modificação de dados mais rápidas e operações de dimensionamento flexíveis. 

Recompilações são necessárias depois:
- Dados são carregados ou modificados
- O armazém de dados é dimensionado para um nível diferente
- Definição da tabela é atualizada

Recompilações não são necessárias após:
- Operação de colocar em pausa
- Retomar a operação

A recompilação não ocorre imediatamente após os dados são modificados. Em vez disso, a recompilação é acionada pela primeira vez que uma consulta seleciona da tabela.  A consulta que disparou a reconstrução lê imediatamente da versão principal da tabela, enquanto os dados de forma assíncrona são copiados para cada nó de computação. Até que a cópia de dados estiver concluída, as consultas subseqüentes vão continuar a utilizar a versão principal da tabela.  Se qualquer atividade acontecer com base na tabela replicada que força a recriação de outra, a cópia de dados é invalidada e a instrução select seguinte irá acionar a dados a copiar novamente. 

### <a name="use-indexes-conservatively"></a>Utilize índices moderadamente
Práticas padrão de indexação aplicam-se para as tabelas replicadas. O SQL Data Warehouse recria cada índice de tabela replicada como parte da reconstrução. Utilize índices apenas quando o ganho de desempenho prevalece sobre o custo de reconstruir os índices.  
 
### <a name="batch-data-loads"></a>Cargas de dados do batch
Ao carregar os dados em tabelas replicadas, tente minimizar recompilações pela criação de batches cargas em conjunto. Execute todos os carregamentos em lote antes de executar as instruções select.

Por exemplo, este padrão de carga carrega dados de quatro origens e invoca quatro recompilações. 

- Carregar a partir de 1 de origem.
- Acionadores de instrução SELECT reconstruir 1.
- Carregar a partir de origem 2.
- Acionadores de instrução SELECT reconstruir 2.
- Carregar a partir de 3 de origem.
- Acionadores de instrução SELECT reconstruir 3.
- Carregar a partir da origem de 4.
- Acionadores de instrução SELECT reconstruir 4.

Por exemplo, este padrão de carga carrega dados de origens de quatro, mas apenas invoca uma reconstrução.

- Carregar a partir de 1 de origem.
- Carregar a partir de origem 2.
- Carregar a partir de 3 de origem.
- Carregar a partir da origem de 4.
- Reconstruir os acionadores de instrução SELECT.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Reconstruir tabelas replicadas após uma carga de batch
Para garantir que os tempos de execução de consulta consistente, considere forçar a compilação das tabelas replicadas após uma carga de batch. Caso contrário, a primeira consulta ainda irá utilizar o movimento de dados para concluir a consulta. 

Esta consulta utiliza a [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV para listar as tabelas replicadas que modificado, mas não reconstruídas.

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
 
Para acionar uma reconstrução, execute a seguinte instrução em cada tabela na saída do anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Passos Seguintes 
Para criar uma tabela replicada, utilize uma das afirmações a seguir:

- [Criar tabela (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Criar TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Para uma descrição geral de tabelas distribuídas, consulte [tabelas distribuídas](sql-data-warehouse-tables-distribute.md).



