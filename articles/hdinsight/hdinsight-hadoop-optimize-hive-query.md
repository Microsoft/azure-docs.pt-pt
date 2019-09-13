---
title: Otimizar consultas de Hive no Azure HDInsight
description: Este artigo descreve como otimizar suas consultas de Apache Hive para o Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.openlocfilehash: d545cd997b35cfa5e7fec58b17507ce63097fd20
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898769"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Otimizar Apache Hive consultas no Azure HDInsight

No Azure HDInsight, há vários tipos de cluster e tecnologias que podem executar Apache Hive consultas. Ao criar seu cluster HDInsight, escolha o tipo de cluster apropriado para ajudar a otimizar o desempenho para suas necessidades de carga de trabalho.

Por exemplo, escolha tipo de cluster de **consulta interativa** para otimizar para consultas ad hoc e interativas. Escolha o tipo de cluster apache **Hadoop** para otimizar as consultas do hive usadas como um processo em lote. Os tipos de cluster **Spark** e **HBase** também podem executar consultas Hive. Para obter mais informações sobre como executar consultas do hive em vários tipos de cluster HDInsight, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Os clusters HDInsight do tipo de cluster Hadoop não são otimizados para desempenho por padrão. Este artigo descreve alguns dos métodos mais comuns de otimização de desempenho do hive que você pode aplicar às suas consultas.

## <a name="scale-out-worker-nodes"></a>Expandir nós de trabalho

Aumentar o número de nós de trabalho em um cluster HDInsight permite que o trabalho Aproveite mais Mapeadores e redutores para serem executados em paralelo. Há duas maneiras de aumentar a escala horizontal no HDInsight:

* No momento em que você cria um cluster, você pode especificar o número de nós de trabalho usando o portal do Azure, Azure PowerShell ou a interface de linha de comando.  Para obter mais informações, veja [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters do HDInsight). A captura de tela a seguir mostra a configuração do nó de trabalho no portal do Azure:
  
    ![scaleout_1](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-scaleout-1.png "scaleout_1")
    
* Após a criação, você também pode editar o número de nós de trabalho para expandir ainda mais um cluster sem recriar um:

    ![scaleout_2](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-scaleout-2.png "scaleout_2")

Para obter mais informações sobre o dimensionamento do HDInsight, consulte [dimensionar clusters hdinsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Usar Apache Tez em vez da redução de mapa

[Apache tez](https://tez.apache.org/) é um mecanismo de execução alternativo para o mecanismo MapReduce. Os clusters HDInsight baseados em Linux têm o tez habilitado por padrão.

![tez_1](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

O tez é mais rápido porque:

* **Execute o DAG (grafo direcionado acíclico) como um único trabalho no mecanismo MapReduce**. O DAG requer que cada conjunto de Mapeadores seja seguido por um conjunto de redutores. Isso faz com que vários trabalhos MapReduce sejam rotacionados para cada consulta de Hive. Tez não tem essa restrição e pode processar DAG complexas como um trabalho, minimizando a sobrecarga de inicialização do trabalho.
* **Evita gravações desnecessárias**. Vários trabalhos são usados para processar a mesma consulta de Hive no mecanismo MapReduce. A saída de cada trabalho MapReduce é gravada no HDFS para dados intermediários. Como o tez minimiza o número de trabalhos para cada consulta do hive, ele é capaz de evitar gravações desnecessárias.
* **Minimiza atrasos de inicialização**. O tez é mais capaz de minimizar o atraso de inicialização, reduzindo o número de Mapeadores que ele precisa para iniciar e também aprimorando a otimização em todo o tempo.
* **Reutiliza contêineres**. Sempre que possível, o tez é capaz de reutilizar contêineres para garantir que a latência devido à inicialização de contêineres seja reduzida.
* **Técnicas de otimização contínua**. Tradicionalmente, a otimização foi feita durante a fase de compilação. No entanto, mais informações sobre as entradas estão disponíveis que permitem uma melhor otimização durante o tempo de execução. O tez usa técnicas de otimização contínua que permitem otimizar o plano ainda mais na fase de tempo de execução.

Para obter mais informações sobre esses conceitos, consulte [Apache tez](https://tez.apache.org/).

Você pode tornar qualquer consulta de Hive tez habilitada prefixando a consulta com o seguinte comando Set:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Particionamento do hive

As operações de e/s são o principal afunilamento de desempenho para executar consultas do hive. O desempenho poderá ser melhorado se a quantidade de dados que precisam ser lidos puder ser reduzida. Por padrão, as consultas do hive examinam todas as tabelas do hive. No entanto, para consultas que precisam apenas verificar uma pequena quantidade de dados (por exemplo, consultas com filtragem), esse comportamento cria sobrecarga desnecessária. O particionamento do hive permite que as consultas do hive acessem apenas a quantidade necessária de dados nas tabelas do hive.

O particionamento do hive é implementado reorganizando os dados brutos em novos diretórios. Cada partição tem seu próprio diretório de arquivos. O particionamento é definido pelo usuário. O diagrama a seguir ilustra o particionamento de uma tabela Hive pelo *ano*da coluna. Um novo diretório é criado para cada ano.

![Particionamento do hive](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Algumas considerações sobre particionamento:

* **Não em** particionamento de partição em colunas com apenas alguns valores podem causar poucas partições. Por exemplo, o particionamento no gênero cria apenas duas partições a serem criadas (macho e fêmea), reduzindo, portanto, apenas a latência por um máximo de metade.
* **Não por meio de partição** – no outro extremo, a criação de uma partição em uma coluna com um valor exclusivo (por exemplo, userid) causa várias partições. A partição over causa muita sobrecarga no namenode do cluster, pois ele precisa lidar com o grande número de diretórios.
* **Evitar distorção de dados** – escolha a chave de particionamento de forma inteligente para que todas as partições sejam de tamanho par. Por exemplo, o particionamento na coluna *estado* pode distorcer a distribuição de dados. Como o estado da Califórnia tem uma população quase 30 vezes mais que a Vermont, o tamanho da partição é potencialmente distorcido e o desempenho pode variar enormemente.

Para criar uma tabela de partição, use a cláusula *particionada por* :

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Depois que a tabela particionada for criada, você poderá criar particionamento estático ou particionamento dinâmico.

* **Particionamento estático** significa que você já fragmentou dados nos diretórios apropriados. Com partições estáticas, você adiciona partições do hive manualmente com base no local do diretório. O trecho de código a seguir é um exemplo.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* O **particionamento dinâmico** significa que você deseja que o hive crie partições automaticamente para você. Como você já criou a tabela de particionamento da tabela de preparo, tudo o que você precisa fazer é inserir dados na tabela particionada:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Para obter mais informações, consulte [tabelas particionadas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Usar o formato ORCFile
O hive dá suporte a diferentes formatos de arquivo. Por exemplo:

* **Text**: o formato de arquivo padrão e funciona com a maioria dos cenários.
* **Avro**: funciona bem para cenários de interoperabilidade.
* **Orc/parquet**: mais adequado para desempenho.

O formato ORC (coluna de linha otimizada) é uma maneira altamente eficiente de armazenar dados do hive. Em comparação com outros formatos, o ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo DateTime e tipos complexos e semiestruturados.
* até 70% de compactação.
* indexa a cada 10.000 linhas, que permitem ignorar linhas.
* uma queda significativa na execução em tempo de execução.

Para habilitar o formato ORC, primeiro crie uma tabela com a cláusula *armazenada como Orc*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Em seguida, insira dados na tabela ORC da tabela de preparo. Por exemplo:

   ```hive
   INSERT INTO TABLE lineitem_orc
   SELECT L_ORDERKEY as L_ORDERKEY, 
          L_PARTKEY as L_PARTKEY , 
          L_SUPPKEY as L_SUPPKEY,
          L_LINENUMBER as L_LINENUMBER,
          L_QUANTITY as L_QUANTITY, 
          L_EXTENDEDPRICE as L_EXTENDEDPRICE,
          L_DISCOUNT as L_DISCOUNT,
          L_TAX as L_TAX,
          L_RETURNFLAG as L_RETURNFLAG,
          L_LINESTATUS as L_LINESTATUS,
          L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;
   ```
   
Você pode ler mais sobre o formato ORC no [manual do idioma Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vetorização

A vetorização permite que o hive processe um lote de 1024 linhas em vez de processar uma linha por vez. Isso significa que operações simples são feitas mais rapidamente porque menos código interno precisa ser executado.

Para habilitar a vetorização, Prefixe sua consulta de Hive com a seguinte configuração:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Para obter mais informações, consulte [execução de consulta em vetor](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Outros métodos de otimização
Há mais métodos de otimização que você pode considerar, por exemplo:

* **Bucketing do hive:** uma técnica que permite clusterizar ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
* **Otimização de junção:** otimização do planejamento de execução de consulta do hive para melhorar a eficiência das junções e reduzir a necessidade de dicas de usuário. Para obter mais informações, consulte [otimização de junção](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumentar**os redutores.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu vários métodos comuns de otimização de consulta do hive. Para saber mais, confira os seguintes artigos:

* [Usar Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Analisar dados de atraso de voo usando a consulta interativa no HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Analisar dados do Twitter usando o Apache Hive no HDInsight](hdinsight-analyze-twitter-data-linux.md)
