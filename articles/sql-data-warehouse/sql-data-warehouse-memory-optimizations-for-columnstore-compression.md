---
title: Melhorar o desempenho do índice columnstore
description: O Azure SQL Data Warehouse reduzir os requisitos de memória ou aumentar a memória disponível para maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d5dba4e9a086502f638252a0ce2b16b4abeeb643
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685654"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximizando a qualidade do rowgroup para columnstore

A qualidade do rowgroup é determinada pelo número de linhas em um rowgroup. Aumentar a memória disponível pode maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.  Use esses métodos para melhorar as taxas de compactação e o desempenho de consulta para índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do rowgroup é importante
Como um índice columnstore examina uma tabela examinando os segmentos de coluna de RowGroups individuais, maximizar o número de linhas em cada rowgroup aprimora o desempenho da consulta. Quando os RowGroups têm um número alto de linhas, a compactação de dados melhora, o que significa que há menos dados a serem lidos do disco.

Para obter mais informações sobre grupos de colunas, consulte [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para RowGroups
Para melhor desempenho de consulta, o objetivo é maximizar o número de linhas por rowgroup em um índice columnstore. Um rowgroup pode ter um máximo de 1.048.576 linhas. Não há problema para não ter o número máximo de linhas por rowgroup. Os índices Columnstore atingem um bom desempenho quando os RowGroups têm pelo menos 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Os RowGroups podem ser cortados durante a compactação

Durante um carregamento em massa ou uma recompilação de índice columnstore, às vezes não há memória suficiente disponível para compactar todas as linhas designadas para cada rowgroup. Quando há pressão de memória, os índices columnstore cortam os tamanhos de rowgroup para que a compactação no columnstore possa ter sucesso. 

Quando não há memória suficiente para compactar pelo menos 10.000 linhas em cada rowgroup, SQL Data Warehouse gera um erro.

Para obter mais informações sobre o carregamento em massa, consulte [carregamento em massa em um índice columnstore clusterizado](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorar a qualidade do rowgroup

O DMV sys. dm_pdw_nodes_db_column_store_row_group_physical_stats ([Sys. dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) contém a definição de exibição correspondente ao banco de dados SQL para SQL data warehouse) que expõe informações úteis, como o número de linhas em RowGroups e o motivo para aparar se houver corte. Você pode criar a exibição a seguir como uma maneira útil de consultar essa DMV para obter informações sobre a filtragem de rowgroup.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

O trim_reason_desc informa se o rowgroup foi cortado (trim_reason_desc = NO_TRIM implica que não havia corte e o grupo de linhas é de qualidade ideal). Os seguintes motivos de corte indicam o corte prematuro do rowgroup:
- CARREGAMENTO em massa: esse motivo de corte é usado quando o lote de entrada de linhas para a carga tinha menos de 1 milhão linhas. O mecanismo criará grupos de linhas compactados se houver mais de 100.000 linhas sendo inseridas (em vez de inserir no armazenamento Delta), mas definirá o motivo do corte como carregamento em massa. Nesse cenário, considere aumentar a carga do lote para incluir mais linhas. Além disso, reavalie seu esquema de particionamento para garantir que ele não seja muito granular, pois grupos de linhas não podem abranger limites de partição.
- MEMORY_LIMITATION: para criar grupos de linhas com 1 milhão linhas, uma determinada quantidade de memória de trabalho é exigida pelo mecanismo. Quando a memória disponível da sessão de carregamento for menor que a memória de trabalho necessária, os grupos de linhas serão cortados prematuramente. As seções a seguir explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: esse motivo de corte indica que a recorte de rowgroup ocorreu porque havia pelo menos uma coluna de cadeia de caracteres com cadeias de cardinalidade amplas e/ou altas. O tamanho do dicionário é limitado a 16 MB de memória e, quando esse limite é atingido, o grupo de linhas é compactado. Se você se deparar com essa situação, considere isolar a coluna problemática em uma tabela separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

A memória máxima necessária para compactar um rowgroup é aproximadamente

- 72 MB +
- linhas de \#\* \#colunas \* 8 bytes +
- \#linhas \* \#colunas de cadeia de caracteres curta \* 32 bytes +
- \#colunas de cadeia de caracteres longa \* 16 MB para o dicionário de compactação

onde as colunas de cadeia de caracteres curta usam tipos de dados de cadeia de caracteres de < = 32 bytes e colunas de cadeia de caracteres longa usam tipos de dados de cadeia de caracteres de > 32 bytes.

Cadeias de caracteres longas são compactadas com um método de compactação projetado para comprimir o texto. Esse método de compactação usa um *dicionário* para armazenar padrões de texto. O tamanho máximo de um dicionário é 16 MB. Há apenas um dicionário para cada coluna de cadeia de caracteres longa no rowgroup.

Para uma discussão aprofundada sobre os requisitos de memória columnstore, consulte o vídeo [Azure SQL data warehouse dimensionamento: configuração e diretrizes](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Maneiras de reduzir os requisitos de memória

Use as técnicas a seguir para reduzir os requisitos de memória para compactar grupos de itens em índices columnstore.

### <a name="use-fewer-columns"></a>Usar menos colunas
Se possível, crie a tabela com menos colunas. Quando um rowgroup é compactado no columnstore, o índice columnstore compacta cada segmento de coluna separadamente. Portanto, os requisitos de memória para compactação de um rowgroup aumentam conforme o número de colunas aumenta.


### <a name="use-fewer-string-columns"></a>Usar menos colunas de cadeia de caracteres
Colunas de tipos de dados de cadeia de caracteres exigem mais memória do que tipos de dados numéricos e de data. Para reduzir os requisitos de memória, considere remover colunas de cadeia de caracteres de tabelas de fatos e colocá-las em tabelas de dimensões menores.

Requisitos de memória adicionais para compactação de cadeia de caracteres:

- Os tipos de dados de cadeia de caracteres com até 32 caracteres podem exigir 32 bytes adicionais por valor.
- Tipos de dados de cadeia de caracteres com mais de 32 caracteres são compactados usando métodos de dicionário.  Cada coluna no rowgroup pode exigir até 16 MB adicionais para criar o dicionário.

### <a name="avoid-over-partitioning"></a>Evitar o excesso de particionamento

Os índices Columnstore criam um ou mais grupos de rowgroup por partição. Em SQL Data Warehouse, o número de partições aumenta rapidamente, pois os dados são distribuídos e cada distribuição é particionada. Se a tabela tiver muitas partições, talvez não haja linhas suficientes para preencher os RowGroups. A falta de linhas não cria a pressão de memória durante a compactação, mas leva a grupos que não atingem o melhor desempenho de consulta columnstore.

Outro motivo para evitar o excesso de particionamento é que há uma sobrecarga de memória para carregar linhas em um índice columnstore em uma tabela particionada. Durante uma carga, muitas partições podem receber as linhas de entrada, que são mantidas na memória até que cada partição tenha linhas suficientes para ser compactada. Ter muitas partições cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carregamento

O banco de dados compartilha a concessão de memória para uma consulta entre todos os operadores na consulta. Quando uma consulta de carregamento tem classificações e junções complexas, a memória disponível para compactação é reduzida.

Projete a consulta de carregamento para se concentrar apenas no carregamento da consulta. Se você precisar executar transformações nos dados, execute-os separadamente da consulta de carregamento. Por exemplo, preparar os dados em uma tabela de heap, executar as transformações e, em seguida, carregar a tabela de preparo no índice columnstore. Você também pode carregar os dados primeiro e, em seguida, usar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribuição compacta os RowGroups no columnstore em paralelo quando há mais de um núcleo de CPU disponível por distribuição. O paralelismo requer recursos de memória adicionais, o que pode levar à pressão de memória e ao corte de rowgroup.

Para reduzir a pressão de memória, você pode usar a dica de consulta MAXDOP para forçar a operação de carregamento a ser executada no modo Serial dentro de cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Maneiras de alocar mais memória

O tamanho do DWU e a classe de recurso de usuário juntos determinam a quantidade de memória disponível para uma consulta de usuário. Para aumentar a concessão de memória para uma consulta de carregamento, você pode aumentar o número de DWUs ou aumentar a classe de recurso.

- Para aumentar o DWUs, consulte [como fazer dimensionar o desempenho?](quickstart-scale-compute-portal.md)
- Para alterar a classe de recurso de uma consulta, consulte [alterar um exemplo de classe de recurso de usuário](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Passos seguintes

Para encontrar mais maneiras de melhorar o desempenho no SQL Data Warehouse, consulte a [visão geral de desempenho](sql-data-warehouse-overview-manage-user-queries.md).

