---
title: Ajuste de desempenho com o Azure SQL Data Warehouse índice columnstore clusterizado ordenado | Microsoft Docs
description: Recomendações e considerações que você deve saber ao usar o índice columnstore clusterizado ordenado para melhorar o desempenho da consulta.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 74a1a2218020718a05c9d01de96ddf4fccb35eb4
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802576"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ajuste de desempenho com o índice columnstore clusterizado ordenado  

Quando os usuários consultam uma tabela columnstore no Azure SQL Data Warehouse, o otimizador verifica os valores mínimo e máximo armazenados em cada segmento.  Os segmentos que estão fora dos limites do predicado de consulta não são lidos do disco para a memória.  Uma consulta pode obter um desempenho mais rápido se o número de segmentos a serem lidos e seu tamanho total forem pequenos.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Índice columnstore clusterizado versus não ordenado 
Por padrão, para cada tabela de data warehouse do Azure criada sem uma opção de índice, um componente interno (Construtor de índice) cria um CCI (índice columnstore clusterizado) não ordenado.  Os dados em cada coluna são compactados em um segmento de rowgroup de CCI separado.  Há metadados no intervalo de valores de cada segmento, de modo que os segmentos que estão fora dos limites do predicado de consulta não são lidos do disco durante a execução da consulta.  O CCI oferece o nível mais alto de compactação de dados e reduz o tamanho dos segmentos a serem lidos para que as consultas possam ser executadas mais rapidamente. No entanto, como o construtor de índice não classifica os dados antes de compactá-los em segmentos, os segmentos com intervalos de valores sobrepostos podem ocorrer, fazendo com que as consultas leiam mais segmentos do disco e demorem mais para serem concluídas.  

Ao criar um CCI ordenado, o mecanismo de SQL Data Warehouse do Azure classifica os dados na memória pelas chaves de ordem antes que o construtor de índice o compacte em segmentos de índice.  Com os dados classificados, a sobreposição de segmento é reduzida, permitindo que as consultas tenham uma eliminação de segmento mais eficiente e um desempenho mais rápido, pois o número de segmentos a serem lidos do disco é menor.  Se todos os dados puderem ser classificados na memória de uma vez, a sobreposição de segmento poderá ser evitada.  Considerando o grande tamanho dos dados em data warehouse tabelas, esse cenário não acontece com frequência.  

Para verificar os intervalos de segmento de uma coluna, execute este comando com o nome da tabela e o nome da coluna:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

## <a name="data-loading-performance"></a>Desempenho do carregamento de dados

O desempenho do carregamento de dados em uma tabela de CCI ordenada é semelhante ao carregamento de dados em uma tabela particionada.  
Carregar dados em uma tabela CCI ordenada pode levar mais tempo do que o carregamento de dados em uma tabela CCI não ordenada devido à classificação de dados.  

Aqui está um exemplo de comparação de desempenho de carregamento de dados em tabelas com esquemas diferentes.
![Performance_comparison_data_loading @ no__t-1
 
## <a name="reduce-segment-overlapping"></a>Reduzir a sobreposição de segmento
Abaixo estão as opções para reduzir ainda mais a sobreposição de segmento ao criar um CCI ordenado em uma nova tabela por meio de CTAS ou em uma tabela existente com dados:

- Use uma classe de recursos maior para permitir que mais dados sejam classificados de uma só vez na memória antes que o construtor de índice os compacte em segmentos.  Uma vez em um segmento de índice, o local físico dos dados não pode ser alterado.  Não há nenhuma classificação de dados dentro de um segmento ou entre segmentos.  

- Use um grau menor de paralelismo (DOP = 1, por exemplo).  Cada thread usado para a criação de CCI ordenada funciona em um subconjunto de dados e o classifica localmente.  Não há nenhuma classificação global entre os dados classificados por threads diferentes.  O uso de threads paralelos pode reduzir o tempo de criação de um CCI ordenado, mas irá gerar mais segmentos sobrepostos do que usar um único thread. 
- Classifique previamente os dados pelas chaves de classificação antes de carregá-los nas tabelas SQL Data Warehouse do Azure.

## <a name="create-ordered-cci-on-large-tables"></a>Criar CCI ordenado em tabelas grandes
A criação de um CCI ordenado é uma operação offline.  Para tabelas sem partições, os dados não poderão ser acessados pelos usuários até que o processo de criação de CCI ordenado seja concluído.   Para tabelas particionadas, como o mecanismo cria a partição de CCI ordenada por partição, os usuários ainda podem acessar os dados em partições em que a criação de CCI ordenada não está em processo.   Você pode usar essa opção para minimizar o tempo de inatividade durante a criação ordenada de CCI em tabelas grandes: 

1.  Crie partições na tabela de destino grande (chamada tabela A).
2.  Crie uma tabela de CCI ordenada vazia (chamada tabela B) com a mesma tabela e esquema de partição que a tabela A.
3.  Mude uma partição da tabela A para a tabela B.
4.  Execute ALTER INDEX < Ordered_CCI_Index > Rebuild na tabela B para recriar a partição alternada.  
5.  Repita as etapas 3 e 4 para cada partição na tabela A.
6.  Depois que todas as partições forem alternadas da tabela A para a tabela B e tiverem sido recriadas, remova A tabela A e renomeie a tabela B para a tabela A. 

## <a name="examples"></a>Exemplos

**UM. Para verificar as colunas ordenadas e o ordinal do pedido:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Para alterar a coluna ordinal, adicione ou remova colunas da lista Order ou altere de CCI para CCI ordenado:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
