---
title: Melhorar o desempenho do índice da loja de colunas
description: Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas dentro de cada grupo de remo.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8e78ad26701bae1357ef6a2a0a03dff1319f0efe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633173"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximizando a qualidade do grupo de remo para a loja de colunas

A qualidade do grupo de remo é determinada pelo número de linhas num grupo de remo. Aumentar a memória disponível pode maximizar o número de linhas que um índice de colunas comprime em cada grupo de remo.  Utilize estes métodos para melhorar as taxas de compressão e o desempenho da consulta para os índices de colunas.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do grupo de remo importa
Uma vez que um índice de colunas digitaliza uma tabela digitalizando segmentos de colunas de grupos de linhas individuais, maximizar o número de linhas em cada grupo de remo aumenta o desempenho da consulta. 

Quando os grupos de remo têm um elevado número de linhas, a compressão de dados melhora o que significa que há menos dados para ler a partir do disco.

Para mais informações sobre grupos de remo, consulte [columnstore Indexes Guide](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho do alvo para grupos de remo
Para um melhor desempenho de consulta, o objetivo é maximizar o número de linhas por grupo de remo num índice de colunas. Um grupo de remo pode ter um máximo de 1.048.576 linhas. 

Não faz mal não ter o número máximo de filas por grupo. Os índices de colunas obtêm um bom desempenho quando os grupos de remo têm pelo menos 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Os grupos de remo podem ser aparados durante a compressão

Durante uma recompressão do índice de carga a granel ou de uma loja de colunas, às vezes não há memória suficiente para comprimir todas as linhas designadas para cada grupo de remo. Quando a pressão da memória está presente, os índices da loja de colunas aparam os tamanhos do grupo de remo para que a compressão na loja de colunas possa ter sucesso. 

Quando não houver memória suficiente para comprimir pelo menos 10.000 linhas em cada grupo de remo, um erro será gerado.

Para obter mais informações sobre o carregamento a granel, consulte a carga a granel num índice de loja de [colunas agrupada](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorizar a qualidade do grupo de remo

O DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats[(sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) contém a definição de vista correspondente ao SQL DB) que expõe informações úteis, tais como o número de linhas em grupos de remo e a razão para aparar, se houver aparar. 

Pode criar a seguinte vista como uma forma útil de consultar este DMV para obter informações sobre aparas de grupo de remo.

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

O trim_reason_desc diz se o grupo de remo foi aparado (trim_reason_desc = NO_TRIM implica que não houve aparas e o grupo de remo é de qualidade ideal). As seguintes razões de corte indicam aparas prematuras do grupo de remo:
- CARGA BULK: Esta razão de corte é utilizada quando o lote de linhas de entrada para a carga tinha menos de 1 milhão de linhas. O motor criará grupos de linhas comprimidos se houver mais de 100.000 linhas inseridas (em oposição à inserção na loja delta), mas define o motivo de corte para a CARGA BULK. Neste cenário, considere aumentar a carga do lote para incluir mais linhas. Além disso, reavalie o seu esquema de partição para garantir que não é muito granular, uma vez que os grupos de remo não podem abranger os limites da partilha.
- MEMORY_LIMITATION: Para criar grupos de remo com 1 milhão de linhas, é necessária uma certa quantidade de memória de trabalho pelo motor. Quando a memória disponível da sessão de carregamento é inferior à memória de trabalho necessária, os grupos de remo são aparados prematuramente. As seguintes secções explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Esta razão de corte indica que o aparador de remo ocorreu porque havia pelo menos uma coluna de cordas com cordas de cardinalidade largas e/ou altas. O tamanho do dicionário é limitado a 16 MB na memória e uma vez atingido este limite, o grupo de remo é comprimido. Se encontrar esta situação, considere isolar a coluna problemática numa mesa separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

A memória máxima necessária para comprimir um grupo de remo é aproximadamente

- 72 MB +
- \#linhas \* \#colunas \* 8 bytes +
- \#linhas de cadeias curtas \* colunas 32 bytes + \* \#
- \#colunas \* de longas cordas 16 MB para dicionário de compressão

> [!NOTE]
> As colunas de cadeias curtas utilizam tipos de dados de cadeias de <= 32 bytes e colunas de cordas longas usam tipos de dados de cadeias de > 32 bytes.

As cordas compridas são comprimidas com um método de compressão concebido para comprimir o texto. Este método de compressão usa um *dicionário* para armazenar padrões de texto. O tamanho máximo de um dicionário é de 16 MB. Há apenas um dicionário para cada longa coluna de cordas no grupo de remo.

Para uma discussão aprofundada dos requisitos de memória da loja de colunas, consulte o vídeo [Synapse SQL: configuração e orientação](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Formas de reduzir os requisitos de memória

Utilize as seguintes técnicas para reduzir os requisitos de memória para comprimir grupos de linhas em índices de lojas de colunas.

### <a name="use-fewer-columns"></a>Use menos colunas
Se possível, desenhe a tabela com menos colunas. Quando um grupo de remo é comprimido na loja de colunas, o índice da coluna comprime cada segmento de coluna sem parar. 

Como tal, os requisitos de memória para comprimir um grupo de remo aumentam à medida que o número de colunas aumenta.


### <a name="use-fewer-string-columns"></a>Use menos colunas de cordas
Colunas de tipos de dados de cordas requerem mais memória do que tipos de dados numéricos e datais. Para reduzir os requisitos de memória, considere remover as colunas de cordas das tabelas de factos e colocá-las em tabelas de dimensão mais pequenas.

Requisitos adicionais de memória para a compressão de cordas:

- Os tipos de dados de cadeia até 32 caracteres podem exigir 32 bytes adicionais por valor.
- Os tipos de dados de cordas com mais de 32 caracteres são comprimidos utilizando métodos de dicionário.  Cada coluna no grupo de remo pode exigir até 16 MB adicionais para construir o dicionário.

### <a name="avoid-over-partitioning"></a>Evite a sobre-divisão

Os índices de colunas criam um ou mais grupos de linhas por partição. Para o pool SQL no Azure Synapse Analytics, o número de divisórias cresce rapidamente porque os dados são distribuídos e cada distribuição é dividida. 

Se a mesa tiver demasiadas divisórias, pode não haver filas suficientes para encher os grupos de remo. A falta de linhas não cria pressão de memória durante a compressão. Mas, leva a grupos de remo que não conseguem o melhor desempenho de consulta de colunas.

Outra razão para evitar a sobre-divisão é que há uma sobrecarga de memória para carregar linhas num índice de loja de colunas numa tabela dividida. 

Durante uma carga, muitas divisórias poderiam receber as filas de entrada, que são mantidas na memória até que cada divisória tenha linhas suficientes para ser comprimido. Ter demasiadas divisórias cria pressão adicional de memória.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carga

A base de dados partilha a concessão de memória para uma consulta entre todos os operadores da consulta. Quando uma consulta de carga tem tipos complexos e se junta, a memória disponível para compressão é reduzida.

Desenhe a consulta de carga para se concentrar apenas em carregar a consulta. Se precisar de executar transformações nos dados, separe-os da consulta de carga. Por exemplo, encenar os dados numa tabela de heap, executar as transformações e, em seguida, carregar a tabela de preparação no índice da loja de colunas. 

> [!TIP]
> Também pode carregar os dados primeiro e depois usar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribuição comprime grupos de linhas na loja de colunas em paralelo quando há mais de um núcleo CPU disponível por distribuição. 

O paralelismo requer recursos de memória adicionais, o que pode levar à pressão da memória e ao aparas de grupo de remo.

Para reduzir a pressão de memória, pode utilizar a sugestão de consulta MAXDOP para forçar a operação de carga a funcionar em modo de série dentro de cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Formas de alocar mais memória

O tamanho dWU e a classe de recursos do utilizador em conjunto determinam a quantidade de memória disponível para uma consulta do utilizador. 

Para aumentar a concessão de memória para uma consulta de carga, pode aumentar o número de DWUs ou aumentar a classe de recursos.

- Para aumentar os DWUs, veja como escala o [desempenho?](quickstart-scale-compute-portal.md)
- Para alterar a classe de recursos para uma consulta, consulte Alterar um exemplo de [classe de recursos do utilizador](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Passos seguintes

Para encontrar mais formas de melhorar o desempenho do pool SQL, consulte a visão geral do [Desempenho.](cheat-sheet.md)
