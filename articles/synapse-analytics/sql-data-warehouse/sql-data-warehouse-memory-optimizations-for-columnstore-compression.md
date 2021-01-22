---
title: Melhorar o desempenho do índice de loja de colunas para piscinas SQL dedicadas
description: Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas dentro de cada grupo de linhas em piscina SQL dedicada.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d668c3e505d6849d3cde52d52698a95c1c5647d9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676167"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maximizar a qualidade do grupo de linha para índices de loja de colunas em piscina SQL dedicada 

A qualidade do grupo rowgroup é determinada pelo número de linhas num grupo de linhas. Aumentar a memória disponível pode maximizar o número de linhas que um índice de loja de colunas comprime em cada grupo de linha.  Utilize estes métodos para melhorar as taxas de compressão e o desempenho da consulta para os índices de loja de colunas.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do grupo de linha importa

Uma vez que um índice de loja de colunas digitaliza uma tabela através da digitalização de segmentos de colunas de grupos de linha individuais, maximizar o número de linhas em cada grupo de linha melhora o desempenho da consulta.

Quando os grupos de linha têm um elevado número de linhas, a compressão de dados melhora, o que significa que há menos dados para ler a partir do disco.

Para obter mais informações sobre grupos de linha, consulte o Guia de [Índices de Colunas.](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="target-size-for-rowgroups"></a>Tamanho do alvo para grupos de linha

Para um melhor desempenho de consulta, o objetivo é maximizar o número de linhas por grupo de linha num índice de loja de colunas. Um grupo de remo pode ter um máximo de 1.048.576 linhas.

Não faz mal não ter o número máximo de filas por grupo. Os índices de loja de colunas alcançam um bom desempenho quando os grupos de linha têm pelo menos 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Grupos de linha podem ser aparados durante a compressão

Durante uma reconstrução do índice de carga a granel ou de colunas, por vezes não há memória suficiente disponível para comprimir todas as linhas designadas para cada grupo de fila. Quando a pressão da memória está presente, os índices da loja de colunas aparem os tamanhos do grupo de linha para que a compressão na loja das colunas possa ter sucesso.

Quando não há memória suficiente para comprimir pelo menos 10.000 linhas em cada grupo de linha, será gerado um erro.

Para obter mais informações sobre o carregamento a granel, consulte [a carga a granel num índice de loja de colunas agrupado](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorizar a qualidade do grupo de linha

O DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats[(sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) contém a definição de visualização correspondente ao SQL DB) que expõe informações úteis, como o número de linhas em grupos de linha e a razão para aparar, se houver aparar.

Pode criar a seguinte vista como uma forma útil de consultar este DMV para obter informações sobre o corte de grupos de remo.

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

O trim_reason_desc diz se o grupo de linha foi aparado (trim_reason_desc = NO_TRIM implica que não houve aparar e o grupo de linha é de ótima qualidade). As seguintes razões de corte indicam o corte prematuro do grupo de linha:

- BULKLOAD: Esta razão de corte é utilizada quando o lote de linhas de entrada para a carga tinha menos de 1 milhão de linhas. O motor criará grupos de linha comprimidos se houver mais de 100.000 linhas sendo inseridas (em oposição à inserção na loja delta), mas define a razão de corte para BULKLOAD. Neste cenário, considere aumentar a carga do seu lote para incluir mais linhas. Além disso, reavalie o seu esquema de partição para garantir que não é muito granular, uma vez que os grupos de linha não podem abranger os limites da partição.
- MEMORY_LIMITATION: Para criar grupos de linha com 1 milhão de linhas, uma certa quantidade de memória de trabalho é exigida pelo motor. Quando a memória disponível da sessão de carregamento é inferior à memória de trabalho necessária, os grupos de linha são aparados prematuramente. As secções seguintes explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Esta razão de corte indica que o aparamento do grupo de linha ocorreu porque havia pelo menos uma coluna de cordas com cordas de cardinalidade largas e/ou altas. O tamanho do dicionário é limitado a 16 MB na memória e uma vez que este limite é atingido o grupo de linha é comprimido. Se encontrar esta situação, considere isolar a coluna problemática numa mesa separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

A memória máxima necessária para comprimir um grupo de linha é aproximadamente

- 72 MB +
- \#\* \# linhas colunas \* 8 bytes +
- \#linhas \* \# de curtas-cordas colunas \* 32 bytes +
- \#colunas de longa duração \* 16 MB para dicionário de compressão

> [!NOTE]
> As colunas de cordas curtas utilizam tipos de dados de cordas de <= 32 bytes e colunas de cordas longas utilizam tipos de dados de cordas de > 32 bytes.

As cordas longas são comprimidas com um método de compressão concebido para comprimir texto. Este método de compressão utiliza um *dicionário* para armazenar padrões de texto. O tamanho máximo de um dicionário é de 16 MB. Há apenas um dicionário para cada longa coluna de cordas no grupo de linha.

Para uma discussão aprofundada sobre os requisitos de memória da loja de colunas, consulte o vídeo [SqL Pool Scaling dedicado: configuração e orientação](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Formas de reduzir os requisitos de memória

Utilize as seguintes técnicas para reduzir os requisitos de memória para comprimir grupos de linha em índices de lojas de colunas.

### <a name="use-fewer-columns"></a>Use menos colunas

Se possível, desenhe a tabela com menos colunas. Quando um grupo de linha é comprimido na loja de colunas, o índice de loja de colunas comprime cada segmento de coluna separadamente.

Como tal, os requisitos de memória para comprimir um grupo de linha aumentam à medida que o número de colunas aumenta.

### <a name="use-fewer-string-columns"></a>Use menos colunas de cordas

Colunas de tipos de dados de cadeia requerem mais memória do que tipos de dados numéricos e data. Para reduzir os requisitos de memória, considere remover as colunas de cordas das tabelas de factos e colocá-las em mesas de dimensão mais pequenas.

Requisitos adicionais de memória para a compressão das cordas:

- Os tipos de dados de cadeia até 32 caracteres podem requerer 32 bytes adicionais por valor.
- Os tipos de dados de cadeia com mais de 32 caracteres são comprimidos utilizando métodos de dicionário.  Cada coluna do grupo de linha pode exigir até 16 MB adicionais para construir o dicionário.

### <a name="avoid-over-partitioning"></a>Evite a sobre-partição

Os índices de loja de colunas criam um ou mais grupos de linha por partição. Para piscina SQL dedicada em Azure Synapse Analytics, o número de divisórias cresce rapidamente porque os dados são distribuídos e cada distribuição é dividida.

Se a mesa tiver muitas divisórias, pode não haver filas suficientes para preencher os grupos de filas. A falta de linhas não cria pressão de memória durante a compressão. Mas, leva a grupos de remo que não alcançam o melhor desempenho de consulta de colunas.

Outra razão para evitar a divisão excessiva é que há uma sobrecarga de memória para carregar linhas em um índice de colunas em uma mesa dividida.

Durante uma carga, muitas divisórias poderiam receber as linhas de entrada, que são mantidas na memória até que cada divisória tenha linhas suficientes para ser comprimida. Ter demasiadas divisórias cria pressão adicional na memória.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carga

A base de dados partilha o subsídio de memória para uma consulta entre todos os operadores da consulta. Quando uma consulta de carga tem tipos complexos e une,.

Desenhe a consulta de carga para se concentrar apenas no carregamento da consulta. Se precisar de executar transformações nos dados, execute-os separadamente da consulta de carga. Por exemplo, encenar os dados numa tabela de amontoados, executar as transformações e, em seguida, carregar a tabela de encenação no índice da loja de colunas.

> [!TIP]
> Também pode carregar os dados primeiro e depois usar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribuição comprime grupos de linha na loja de colunas em paralelo quando há mais de um núcleo de CPU disponível por distribuição.

O paralelismo requer recursos de memória adicionais, o que pode levar à pressão da memória e ao corte do grupo de remo.

Para reduzir a pressão da memória, pode utilizar a sugestão de consulta MAXDOP para forçar a operação de carga a funcionar em modo de série dentro de cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Formas de alocar mais memória

O tamanho do DWU e a classe de recursos do utilizador em conjunto determinam a quantidade de memória disponível para uma consulta do utilizador.

Para aumentar o subsídio de memória para uma consulta de carga, pode aumentar o número de DWUs ou aumentar a classe de recursos.

- Para aumentar os DWUs, veja [como dimensionar o desempenho?](quickstart-scale-compute-portal.md)
- Para alterar a classe de recursos para uma consulta, consulte [Alterar um exemplo de classe de recursos do utilizador](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Passos seguintes

Para encontrar mais formas de melhorar o desempenho para uma piscina SQL dedicada, consulte a [visão geral](cheat-sheet.md)do Desempenho.
