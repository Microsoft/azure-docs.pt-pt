---
title: Afinação de desempenho com índice de colunas agrupadas ordenada
description: Recomendações e considerações que deve saber ao usar índice de colunas agrupadas ordenado para melhorar o seu desempenho de consulta.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: abeb5c125a746842f522030878f93941450df974
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200554"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Afinação de desempenho com índice de colunas agrupadas ordenada  

Quando os utilizadores consultam uma tabela de colunas no SQL Analytics, o optimizador verifica os valores mínimos e máximos armazenados em cada segmento.  Segmentos que estão fora dos limites do predicado de consulta não são lidos do disco à memória.  Uma consulta pode obter um desempenho mais rápido se o número de segmentos para ler e seu tamanho total são pequenos.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Índice de lojas de colunas agrupadas não ordenados 
Por predefinição, para cada tabela SQL Analytics criada sem uma opção de índice, um componente interno (construtor de índices) cria um índice de colunas agrupada (CCI) não ordenado.  Os dados de cada coluna são comprimidos num segmento separado do grupo de remo CCI.  Há metadados na gama de valores de cada segmento, por isso segmentos que estão fora dos limites do predicado de consulta não são lidos a partir do disco durante a execução da consulta.  O CCI oferece o mais alto nível de compressão de dados e reduz o tamanho dos segmentos para ler para que as consultas possam correr mais rápido. No entanto, como o construtor de índices não classifica os dados antes de os comprimir em segmentos, podem ocorrer segmentos com gamas de valor sobrepostas, fazendo com que as consultas leiam mais segmentos a partir do disco e demorem mais tempo a terminar.  

Ao criar um CCI ordenado, o motor SQL Analytics classifica os dados existentes na memória pela chave de encomenda(s) antes que o construtor de índices os comprime em segmentos de índice.  Com os dados classificados, a sobreposição de segmentos é reduzida permitindo que as consultas tenham uma eliminação de segmento mais eficiente e, portanto, um desempenho mais rápido porque o número de segmentos a ler a partir do disco é menor.  Se todos os dados puderem ser classificados na memória de uma só vez, então a sobreposição do segmento pode ser evitada.  Dada a grande dimensão dos dados nas tabelas SQL Analytics, este cenário não acontece com frequência.  

Para verificar se o segmento varia para uma coluna, execute este comando com o seu nome de mesa e coluna:

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

> [!NOTE] 
> Numa tabela CCI ordenada, os novos dados resultantes do mesmo lote de DML ou de operações de carregamento de dados estão classificados dentro desse lote, não existe uma triagem global em todos os dados da tabela.  Os utilizadores podem reconstruir o CCI ordenado para classificar todos os dados da tabela.  No SQL Analytics, o índice de colunas REBUILD é uma operação offline.  Para uma mesa dividida, a REBUILD é feita uma partição de cada vez.  Os dados da partição que está a ser reconstruída estão "offline" e indisponíveis até que a REBUILD esteja completa para essa partição. 

## <a name="query-performance"></a>Desempenho de consultas

O ganho de desempenho de uma consulta de um CCI ordenado depende dos padrões de consulta, do tamanho dos dados, da forma como os dados estão bem classificados, da estrutura física dos segmentos, e da Classe DWU e de recursos escolhidos para a execução da consulta.  Os utilizadores devem rever todos estes fatores antes de escolher as colunas de encomenda ao conceber uma tabela CCI ordenada.

Consultas com todos estes padrões normalmente correm mais rápido com CCI ordenado.  
1. As consultas têm igualdade, desigualdade ou intervalo predicados
1. As colunas predicadas e as colunas CCI ordenadas são as mesmas.  
1. As colunas predicadas são utilizadas na mesma ordem que a coluna ordinal das colunas CCI ordenadas.  
 
Neste exemplo, a tabela T1 tem um índice de colunas agrupado encomendado na sequência de Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

O desempenho da consulta 1 pode beneficiar mais do CCI ordenado do que das outras três consultas. 

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

## <a name="data-loading-performance"></a>Desempenho de carregamento de dados

O desempenho do carregamento de dados numa tabela CCI ordenada é semelhante a uma tabela dividida.  O carregamento de dados numa tabela CCI ordenada pode demorar mais tempo do que uma tabela CCI não ordenada devido à operação de triagem de dados, no entanto as consultas podem ser executadas mais rapidamente com o CCI ordenado.  

Aqui está uma comparação de desempenho exemplo de carregar dados em tabelas com diferentes esquemas.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Eis um exemplo de comparação de desempenho entre o CCI e o CCI ordenado.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Reduzir a sobreposição do segmento

O número de segmentos sobrepostos depende da dimensão dos dados para classificar, da memória disponível e do grau máximo de definição de paralelismo (MAXDOP) durante a criação ordenada do CCI. Abaixo estão as opções para reduzir a sobreposição do segmento ao criar o CCI ordenado.

- Utilize a classe de recursos xbiggerc num DWU mais elevado para permitir mais memória para a triagem de dados antes que o construtor de índices comprime os dados em segmentos.  Uma vez num segmento de índice, a localização física dos dados não pode ser alterada.  Não há data seletiva dentro de um segmento ou em segmentos.  

- Crie CCI encomendado com MAXDOP = 1.  Cada fio utilizado para a criação ordenada de CCI trabalha num subconjunto de dados e classifica-os localmente.  Não há classificação global através de dados classificados por diferentes fios.  A utilização de fios paralelos pode reduzir o tempo para criar um CCI encomendado, mas gerará segmentos mais sobrepostos do que usar um único fio.  Atualmente, a opção MAXDOP só é suportada na criação de uma tabela CCI ordenada utilizando o comando CREATE TABLE AS SELECT.  A criação de um CCI ordenado através do CREATE INDEX ou dos comandos CREATE TABLE não suporta a opção MAXDOP. Por exemplo,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Pré-classificar os dados pela chave de classificação(s) antes de os colocar em tabelas SQL Analytics.


Eis um exemplo de uma distribuição ordenada da tabela CCI que tem zero segmento sem sobreposição seguindo recomendações acima. A tabela CCI ordenada é criada numa base de dados DWU1000c via CTAS a partir de uma tabela de 20 GB de heap utilizando MAXDOP 1 e xbiggerc.  O CCI é encomendado numa coluna BIGINT sem duplicados.  

![Segment_No_Overlapping](media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Criar CCI encomendado em mesas grandes
Criar um CCI ordenado é uma operação offline.  Para tabelas sem divisórias, os dados não serão acessíveis aos utilizadores até que o processo de criação de CCI ordenado esteja concluído.   Para as tabelas divididas, uma vez que o motor cria a partição ordenada do CCI por partição, os utilizadores ainda podem aceder aos dados em divisórias onde a criação ordenada do CCI não está em processo.   Pode utilizar esta opção para minimizar o tempo de inatividade durante a criação ordenada do CCI em grandes tabelas: 

1.  Crie divisórias na mesa grande alvo (chamada Table_A).
2.  Crie uma mesa CCI ordenada vazia (chamada Table_B) com a mesma mesa e esquema de partição que a tabela A.
3.  Mude uma divisória do quadro A para a tabela B.
4.  Executar ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> na tabela B para reconstruir a divisória comutada.  
5.  Repita os passos 3 e 4 para cada partição em Table_A.
6.  Uma vez que todas as divisórias são trocadas de Table_A para Table_B e foram reconstruídas, deixe Table_A e mude o nome Table_B para Table_A. 

## <a name="examples"></a>Exemplos

**A. Para verificar se há colunas ordenadas e ordem ordenada:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Alterar a coluna de colunas, adicionar ou remover colunas da lista de encomendas ou mudar do CCI para o CCI ordenado:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
