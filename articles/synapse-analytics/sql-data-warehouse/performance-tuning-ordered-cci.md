---
title: Otimização do desempenho com índice columnstore em cluster ordenado
description: Recomendações e considerações que deve conhecer ao utilizar o índice de loja de colunas agrupados encomendado para melhorar o seu desempenho de consulta em piscinas SQL dedicadas.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ab94a83a64ca9770f0c216ddf42145b262629c6d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598997"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Otimização do desempenho com índice columnstore em cluster ordenado  

Quando os utilizadores consultam uma tabela de lojas de colunas em piscina SQL dedicada, o otimizador verifica os valores mínimos e máximos armazenados em cada segmento.  Segmentos que estão fora dos limites do predicado de consulta não são lidos do disco para a memória.  Uma consulta pode obter um desempenho mais rápido se o número de segmentos para ler e seu tamanho total são pequenos.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Índice de colunas agrupados não encomendado

Por padrão, para cada tabela criada sem uma opção de índice, um componente interno (index builder) cria nele um índice de colunas agrupado não encomendado (CCI).  Os dados de cada coluna são comprimidos num segmento de grupo de linha CCI separado.  Há metadados na gama de valor de cada segmento, por isso os segmentos que estão fora dos limites da consulta predicado não são lidos a partir do disco durante a execução de consultas.  O CCI oferece o mais alto nível de compressão de dados e reduz o tamanho dos segmentos para ler para que as consultas possam correr mais rapidamente. No entanto, como o construtor de índices não classifica dados antes de os comprimir em segmentos, segmentos com gamas de valor sobrepostos podem ocorrer, fazendo com que as consultas leiam mais segmentos a partir do disco e levem mais tempo a terminar.  

Ao criar um CCI ordenado, o motor de piscina SQL dedicado classifica os dados existentes na memória pela ou na tecla de encomenda antes que o construtor de índice os comprima em segmentos de índice.  Com dados classificados, a sobreposição de segmentos é reduzida permitindo que as consultas tenham uma eliminação de segmento mais eficiente e, portanto, um desempenho mais rápido porque o número de segmentos a ler a partir do disco é menor.  Se todos os dados puderem ser classificados na memória de uma só vez, então a sobreposição de segmento pode ser evitada.  Devido a grandes tabelas em armazéns de dados, este cenário não acontece com frequência.  

Para verificar os intervalos de segmento de uma coluna, execute o seguinte comando com o nome da sua mesa e nome da coluna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id;


```

> [!NOTE] 
> Numa tabela de CCI encomendada, os novos dados resultantes do mesmo lote de DML ou operações de carregamento de dados são classificados dentro desse lote, não existindo uma triagem global em todos os dados da tabela.  Os utilizadores podem reconstruir o CCI ordenado para classificar todos os dados na tabela.  Na piscina SQL dedicada, o índice de loja de colunas REBUILD é uma operação offline.  Para uma mesa dividida, o REBUILD é feito uma divisória de cada vez.  Os dados na partição que está a ser reconstruída estão "offline" e indisponíveis até que o REBUILD esteja completo para essa partição. 

## <a name="query-performance"></a>Desempenho de consultas

O ganho de desempenho de uma consulta a partir de um CCI ordenado depende dos padrões de consulta, do tamanho dos dados, do quão bem os dados são classificados, da estrutura física dos segmentos, e da DWU e da classe de recursos escolhidas para a execução de consultas.  Os utilizadores devem rever todos estes fatores antes de escolher as colunas de encomenda ao conceber uma tabela CCI encomendada.

Consultas com todos estes padrões normalmente correm mais rápido com CCI ordenado.  
1. As consultas têm igualdade, desigualdade ou predicados de alcance
1. As colunas predicados e as colunas CCI ordenadas são as mesmas.  
1. As colunas predicados são utilizadas na mesma ordem que a coluna ordinal das colunas CCI ordenadas.  
 
Neste exemplo, a tabela T1 tem um índice de loja de colunas agrupado encomendado na sequência de Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A);

```

O desempenho da consulta 1 pode beneficiar mais do CCI encomendado do que das outras três consultas. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Desempenho do carregamento de dados

O desempenho do carregamento de dados numa tabela de CCI encomendada é semelhante a uma tabela dividida.  O carregamento de dados numa tabela de CCI ordenada pode demorar mais tempo do que uma tabela CCI não encomendada devido à operação de triagem de dados, no entanto as consultas podem ser executadas mais rapidamente depois com CCI encomendado.  

Aqui está um exemplo de comparação de desempenho de carregar dados em tabelas com esquemas diferentes.

![Gráfico de barras que mostra a comparação de desempenho de carregar dados em tabelas com esquemas diferentes.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Aqui está um exemplo de comparação de desempenho de consulta entre CCI e CCI ordenado.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Reduzir a sobreposição de segmentos

O número de segmentos sobrepostos depende do tamanho dos dados para classificar, da memória disponível e do grau máximo de paralelismo (MAXDOP) durante a criação ordenada do CCI. Abaixo estão as opções para reduzir a sobreposição de segmentos ao criar CCI ordenado.

- Utilize a classe de recursos xlargerc num DWU mais elevado para permitir uma maior memória para a triagem de dados antes que o construtor de índice comprima os dados em segmentos.  Uma vez num segmento de índice, a localização física dos dados não pode ser alterada.  Não há triagem de dados dentro de um segmento ou em segmentos.  

- Criar CCI encomendado com MAXDOP = 1.  Cada fio utilizado para a criação de CCI ordenado trabalha num subconjunto de dados e classifica-os localmente.  Não há triagem global entre dados classificados por diferentes fios.  A utilização de fios paralelos pode reduzir o tempo para criar um CCI ordenado, mas gerará mais segmentos sobrepostos do que usando um único fio.  Atualmente, a opção MAXDOP só é suportada na criação de uma tabela CCI ordenada utilizando o comando CREATE TABLE AS SELECT.  A criação de um CCI ordenado através de comandos CREATE INDEX ou CREATE TABLE não suporta a opção MAXDOP. Por exemplo,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Pré-ordenar os dados pela ou das teclas de classificação antes de os colocar em tabelas.

Aqui está um exemplo de uma distribuição ordenada da tabela CCI que tem um segmento zero sobreposto seguindo as recomendações acima. A tabela CCI ordenada é criada numa base de dados DWU1000c via CTAS a partir de uma tabela de pilhas de 20 GB usando MAXDOP 1 e xlargerc.  O CCI é encomendado numa coluna BIGINT sem duplicados.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Criar CCI ordenado em grandes mesas

Criar um CCI ordenado é uma operação offline.  Para tabelas sem divisórias, os dados não serão acessíveis aos utilizadores até que o processo de criação de CCI ordenado esteja concluído.   Para as tabelas divididas, uma vez que o motor cria a partição CCI ordenada por partição, os utilizadores ainda podem aceder aos dados em divisórias onde a criação de CCI ordenada não está em processo.   Pode utilizar esta opção para minimizar o tempo de inatividade durante a criação ordenada do CCI em grandes tabelas: 

1.    Criar divisórias na tabela de grandes alvos (chamada Table_A).
2.    Crie uma mesa CCI vazia (chamada Table_B) com a mesma tabela e esquema de partição que a tabela A.
3.    Mude uma divisória da tabela A para a tabela B.
4.    Executar <Ordered_CCI_Index> DE ÍNDICE ALTER ON <Table_B> RECONSTRUIR PARTIÇÃO = <Partition_ID> na tabela B para reconstruir a partição comutado.  
5.    Repita os passos 3 e 4 para cada partição em Table_A.
6.    Uma vez que todas as divisórias são mudadas de Table_A para Table_B e foram reconstruídas, deixe cair Table_A e mude o nome Table_B para Table_A. 

>[!TIP]
> Para uma mesa de bilhar SQL dedicada com um CCI ordenado, o ALTER INDEX REBUILD irá reclassiturar os dados usando o tempdb. Monitora temperatura durante as operações de reconstrução. Se precisar de mais espaço temporário, escale a piscina. Reduza para baixo assim que o índice estiver concluído.
>
> Para uma mesa de bilhar SQL dedicada com um CCI encomendado, o ALTER INDEX REORGANIZE não reordena os dados. Para recorrer aos dados, utilize o ALTER INDEX REBUILD.
>
> Para obter mais informações sobre a manutenção ordenada do CCI, consulte [otimizar os índices de lojas de colunas agrupadas](sql-data-warehouse-tables-index.md#optimizing-clustered-columnstore-indexes).

## <a name="examples"></a>Exemplos

**A. Para verificar se há colunas e encomendas ordinais encomendados:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0;
```

**B. Para alterar coluna ordinal, adicione ou remova colunas da lista de encomendas, ou para alterar de CCI para CCI ordenado:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON dbo.InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON);
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
