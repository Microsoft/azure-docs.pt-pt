---
title: Melhorar o desempenho de índice columnstore - Azure SQL Data Warehouse | Documentos da Microsoft
description: Reduzir os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que compacta de um índice columnstore em cada rowgroup.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/22/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e7ab09522184f5c2d1c5168b24b2948f58e5189e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368974"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximizando a qualidade de rowgroup para columnstore

Qualidade de Rowgroup é determinada pelo número de linhas num rowgroup. Aumentar a memória disponível pode maximizar o número de linhas que compacta de um índice columnstore em cada rowgroup.  Utilize estes métodos para melhorar as velocidades de compressão e consultar o desempenho de índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho de rowgroup é importante
Uma vez que um índice columnstore analisa uma tabela através da análise de segmentos de coluna de grupos de linhas individuais, maximizar o número de linhas em cada rowgroup aprimora o desempenho de consulta. Quando os grupos de linhas têm um elevado número de linhas, compressão de dados melhora o que significa que há menos dados para ler a partir do disco.

Para obter mais informações sobre grupos de linhas, consulte [guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para grupos de linhas
Para obter melhor desempenho de consulta, o objetivo é maximizar o número de linhas por rowgroup num índice columnstore. Um rowgroup pode ter um máximo de 1 048 576 linhas. Há problema em tem o número máximo de linhas por rowgroup. Os índices Columnstore alcançar o bom desempenho quando os grupos de linhas de tem, pelo menos, 100 000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Grupos de linhas podem obter cortados durante a compressão

Durante uma em massa de carga ou columnstore recompilação de índice, às vezes, lá não existe memória suficiente disponível para comprimir todas as linhas designadas para cada rowgroup. Quando existe pressão de memória, os índices columnstore cortar os tamanhos de rowgroup para que compressão para o columnstore pode ter êxito. 

Quando existe memória suficiente para comprimir, pelo menos, 10 000 linhas em cada rowgroup, o SQL Data Warehouse gera um erro.

Para obter mais informações sobre o carregamento em massa, consulte [carregamento em massa para um índice columnstore em cluster](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorizar a qualidade de rowgroup

A DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) contém a definição de vista que correspondem a BD SQL do armazém de dados SQL) que expõe informações úteis como o número de linhas em grupos de linhas e a razão para remoção de se existir foi corte. Pode criar a vista seguinte como uma forma prática de consultar essa DMV para obter informações sobre a remoção de rowgroup.

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

O trim_reason_desc informa se o rowgroup foi removido (trim_reason_desc = NO_TRIM implica não ocorreu nenhuma limitação e grupo de linhas é de qualidade ideal). Pelas seguintes razões corte indicam prematura corte do rowgroup:
- BULKLOAD: Esta razão de compactação é utilizado quando o lote de entrada de linhas para a carga tinha menos de 1 milhão de linhas. O mecanismo irá criar grupos de linhas comprimido se existirem mais de 100 000 linhas que estão sendo inseridas (em vez de inserção para o arquivo de delta), mas define o motivo de compactação para BULKLOAD. Neste cenário, considere a aumentar a carga de batch para incluir mais linhas. Além disso, a reavaliar o esquema de partição para garantir que não seja muito granular como grupos de linhas não podem abranger os limites de partição.
- MEMORY_LIMITATION: Para criar grupos de linhas com 1 milhão de linhas, uma determinada quantidade de memória de trabalho é necessário pelo motor. Quando a memória disponível da sessão de carregamento é menor do que a memória necessária do trabalho, os grupos de linhas prematuramente obterem cortados. As secções seguintes explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Esta razão corte indica que a remoção de rowgroup ocorreu devido a pelo menos uma coluna de cadeia de caracteres com cadeias de caracteres de e/ou elevada cardinalidade. O tamanho de dicionário é limitado a 16 MB na memória e quando este limite for atingido o grupo de linhas é comprimido. Caso se depare com essa situação, considere o isolamento da coluna problemática numa tabela separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

O máximo de memória necessário para compactar um rowgroup é de aproximadamente

- 72 MB +
- \#linhas \* \#colunas \* 8 bytes +
- \#linhas \* \#colunas de cadeia curta \* 32 bytes +
- \#colunas de cadeia longa \* 16 MB de dicionário de compressão

onde colunas de cadeia curta utilizam tipos de dados de cadeia de caracteres de < = 32 bytes e tipos de dados de cadeia de caracteres de utilização de colunas de cadeia longa de > 32 bytes.

Cadeias de caracteres longas são compactadas com um método de compressão concebido para a compressão de texto. Este método de compressão utiliza um *dicionário* para armazenar padrões de texto. O tamanho máximo de um dicionário é 16 MB. Há apenas um dicionário para cada coluna de cadeia longa no rowgroup.

Para uma visão detalhada dos requisitos de memória de columnstore, veja o vídeo [dimensionamento do Azure SQL Data Warehouse: configuração e documentação de orientação](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Formas de reduzir os requisitos de memória

Utilize as seguintes técnicas para reduzir os requisitos de memória para a compressão de grupos de linhas em índices columnstore.

### <a name="use-fewer-columns"></a>Utilizar menos colunas
Se possível, crie a tabela com menos colunas. Quando um rowgroup é compactado para o columnstore, o índice columnstore compacta cada segmento de coluna em separado. Por conseguinte, os requisitos de memória para comprimir um rowgroup aumentam como o número de colunas aumenta.


### <a name="use-fewer-string-columns"></a>Utilizar menos colunas de cadeia de caracteres
Colunas de tipos de dados de cadeia de caracteres requerem mais memória do que numérico e tipos de dados de data. Para reduzir os requisitos de memória, considere remover colunas de cadeia de caracteres de tabelas de fatos e colocá-las em tabelas de dimensão mais pequenas.

Requisitos de memória adicional para compressão de cadeia de caracteres:

- Tipos de dados de cadeia de caracteres até 32 carateres podem exigir 32 bytes adicionais por valor.
- Tipos de dados de cadeia de caracteres com mais do que 32 carateres são compactados usando métodos de dicionário.  Cada coluna no rowgroup pode exigir até um 16 MB adicional para criar o dicionário.

### <a name="avoid-over-partitioning"></a>Evitar a criação de partições de excesso

Os índices Columnstore criar um ou mais grupos de linhas por partição. No SQL Data Warehouse, o número de partições cresce rapidamente, porque os dados são distribuídos e cada distribuição está particionada. Se a tabela tem demasiadas partições, talvez não haja linhas suficientes para preencher os grupos de linhas. A falta de linhas não cria pressão de memória durante a compressão, mas isso leva a grupos de linhas que não alcance o melhor desempenho de consulta de columnstore.

Outro motivo para evitar o excesso de particionamento é que existe uma memória sobrecarga de carregamento de linhas para um índice columnstore numa tabela particionada. Durante uma carga, muitas partições possam receber as linhas de entrada, que são guardadas na memória até que cada partição tem linhas suficientes para ser comprimido. Ter demasiadas partições cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplifique a consulta de carga

A base de dados compartilha a concessão de memória para uma consulta entre todos os operadores na consulta. Quando uma consulta de carga tem tipos complexos e associações, a memória disponível para compressão é reduzida.

Estruture a consulta de carga para se concentrar apenas a carregar a consulta. Se tiver de executar transformações nos dados, executá-las separadas da consulta de carga. Por exemplo, colocar os dados numa tabela de área dinâmica para dados, execute as transformações e, em seguida, carregar a tabela de testes para o índice columnstore. Pode também carregar os dados pela primeira vez e, em seguida, utilizar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribuição compacta os grupos de linhas para o columnstore em paralelo quando há mais de um núcleo de CPU disponível por distribuição. O paralelismo requer recursos de memória adicional, que podem levar a pressão de memória e remoção de rowgroup.

Para reduzir a pressão de memória, pode utilizar a sugestão de consulta MAXDOP para forçar a operação de carregamento sejam executadas em modo serial dentro de cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Formas de alocar mais memória

Tamanho DWU e a classe de recursos de utilizador em conjunto determinam a quantidade de memória está disponível para uma consulta de utilizador. Para aumentar a concessão de memória para uma consulta de carga, pode aumentar o número de DWUs ou aumentar a classe de recursos.

- Para aumentar as DWUs, consulte [como dimensionar o desempenho?](quickstart-scale-compute-portal.md)
- Para alterar a classe de recursos para uma consulta, consulte [alterar um exemplo de classe de recursos de utilizador](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Passos Seguintes

Para ver mais formas de melhorar o desempenho no SQL Data Warehouse, veja a [descrição geral do desempenho](sql-data-warehouse-overview-manage-user-queries.md).

