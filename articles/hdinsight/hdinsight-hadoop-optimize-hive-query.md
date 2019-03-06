---
title: Otimizar as consultas do Hive no HDInsight do Azure
description: Este artigo descreve como otimizar as consultas do Apache Hive do Hadoop no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: ba332bb0f77dc198abdd76ccf024336788a77a33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435039"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Otimizar as consultas do Apache Hive no HDInsight do Azure

No Azure HDInsight, existem vários tipos de clusters e tecnologias que podem executar consultas do Apache Hive. Ao criar o cluster do HDInsight, escolha o tipo de cluster adequado para o ajudar a otimizar o desempenho para as suas necessidades de carga de trabalho. 

Por exemplo, escolha **Interactive Query** tipo para otimizar consultas ad hoc e interativas de cluster. Escolha Apache **Hadoop** tipo para otimizar consultas do Hive utilizadas como um processo em lotes de cluster. **Spark** e **HBase** tipos de cluster também podem executar consultas do Hive. Para obter mais informações sobre como executar consultas do Hive em vários tipos de cluster do HDInsight, consulte [o que é o Apache Hive e o HiveQL no HDInsight do Azure?](hadoop/hdinsight-use-hive.md).

Clusters do HDInsight do tipo de cluster de Hadoop não estão otimizados para desempenho por predefinição. Este artigo descreve alguns dos métodos mais comuns do Hive desempenho otimização que pode aplicar às suas consultas.

## <a name="scale-out-worker-nodes"></a>Aumentar horizontalmente nós de trabalho

Aumentar o número de nós de trabalho num cluster do HDInsight, permite que o trabalho tirar partido de mais mapeadores e redutores para ser executado em paralelo. Existem duas formas de aumentar de escalamento horizontal no HDInsight:

* No momento ao criar um cluster, pode especificar o número de nós de trabalho com o portal do Azure, o Azure PowerShell ou a interface de linha de comandos.  Para obter mais informações, veja [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters do HDInsight). Captura de ecrã seguinte mostra a função de trabalho de configuração do nó no portal do Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* Após a criação, também pode editar o número de nós de trabalho para aumentar horizontalmente um cluster adicional sem recriar um:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para obter mais informações sobre o dimensionamento do HDInsight, consulte [clusters do HDInsight de dimensionamento](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Utilizar o Apache Tez em vez de reduzir o mapa

[Apache Tez](https://hortonworks.com/hadoop/tez/) é um motor de execução alternativo para o mecanismo de MapReduce. Clusters do HDInsight baseado em Linux têm Tez ativado por predefinição.

![tez_1][image-hdi-optimize-hive-tez_1]

Tez é mais rápido porque:

* **Executar direcionado acíclicos Graph (DAG) como uma tarefa única no mecanismo de MapReduce**. O DAG requer que cada conjunto de mapeadores a ser seguido por um conjunto de redutores. Isso faz com que várias tarefas de MapReduce ser lançámos para cada consulta do Hive. Tez não tem essa restrição e pode processar DAG complexo como uma tarefa, portanto, minimizando a sobrecarga de inicialização de tarefa.
* **Evita escritas desnecessárias**. Várias tarefas são utilizadas para processar a mesma consulta do Hive no mecanismo de MapReduce. O resultado de cada tarefa de MapReduce é escrito no HDFS para dados intermediários. Uma vez que Tez minimiza o número de tarefas para cada consulta de Hive, é capaz de evitar escritas desnecessárias.
* **Minimiza a atrasos de inicialização**. Tez melhor é capaz de minimizar o atraso de arranque, reduzindo o número de mapeadores que precisa para começar e também melhorar em toda a otimização.
* **Reutiliza contentores**. Sempre que possível Tez é capaz de reutilizar contentores para garantir que a latência devido a iniciar contentores é reduzida.
* **Técnicas de otimização contínua**. Tradicionalmente, otimização foi realizada durante a fase de compilação. No entanto, estão disponíveis mais informações sobre as entradas que permitem uma melhor otimização durante o tempo de execução. Tez utiliza técnicas de otimização contínua que lhe permitem otimizar ainda mais o plano para a fase de tempo de execução.

Para obter mais informações sobre estes conceitos, veja [Apache TEZ](https://hortonworks.com/hadoop/tez/).

Pode fazer qualquer consulta do Hive no Tez ativada atribuindo a consulta com o seguinte comando do conjunto:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Ramo de registo de criação de partições

Operações de e/s são o afunilamento de desempenho maior para a execução de consultas do Hive. O desempenho pode ser melhorado se pode ser reduzida a quantidade de dados que precisam ser lida. Por predefinição, consultas do Hive de análise de tabelas do Hive completas. No entanto para consultas que apenas precisam de analisar uma pequena quantidade de dados (por exemplo, consultas com a filtragem), esse comportamento cria desnecessário sobrecarga. A criação de partições de Hive permite consultas do Hive aceder a quantidade necessária de dados em tabelas do Hive.

A criação de partições de Hive é implementada por reorganizar os dados não processados em novos diretórios. Cada partição tem seu próprio diretório de ficheiros. A criação de partições é definida pelo utilizador. O diagrama seguinte ilustra a criação de partições de uma tabela do Hive a coluna *ano*. Um novo diretório é criado para cada ano.

![Ramo de registo de criação de partições][image-hdi-optimize-hive-partitioning_1]

Algumas considerações sobre a criação de partições:

* **Fazer não em partição** -criação de partições em colunas com apenas alguns valores pode fazer com que algumas partições. Por exemplo, a criação de partições no sexo apenas cria duas partições para ser criado (masculino e feminino), assim, reduzir a latência apenas por um máximo de metade.
* **Fazer não através da partição** - no outro extremo, criação de uma partição numa coluna com um valor exclusivo (por exemplo, userid) faz com que várias partições. Ao longo da partição faz com que muito esforço no namenode cluster porque esta tem de lidar com grande número de diretórios.
* **Evitar a distorção de dados** -escolha com sabedoria sua chave de particionamento, para que todas as partições são até mesmo tamanho. Por exemplo, criação de partições num *estado* coluna pode distorcer a distribuição de dados. Uma vez que o estado da Califórnia tem uma população quase 30 x que de Vermont, o tamanho da partição é potencialmente desviado e o desempenho podem variar enormemente.

Para criar uma tabela de partição, utilize o *particionados por* cláusula:

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
   
Depois de criar a tabela particionada, pode criar particionamento estático ou dinâmico de criação de partições.

* **Particionamento estático** significa que tenha dados já em partição horizontal nos diretórios apropriados. Com partições estáticas, adicione manualmente com base na localização do diretório de partições de ramo de registo. O fragmento de código seguinte é um exemplo.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Particionamento dinâmico** significa que pretende que o Hive para criar partições automaticamente para. Uma vez que já criou a tabela de criação de partições da tabela de teste, tudo o que precisa fazer é inserir dados para a tabela particionada:
  
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
   
Para obter mais informações, consulte [tabelas Particionadas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Utilize o formato de ORCFile
Hive suporta formatos de ficheiro diferente. Por exemplo:

* **Texto**: o formato de arquivo padrão e funciona com a maioria dos cenários.
* **Avro**: funciona bem para cenários de interoperabilidade.
* **ORC/Parquet**: mais adequada para o desempenho.

Formato ORC (com otimização de linha em colunas) é uma maneira altamente eficiente para armazenar os dados de Hive. Em comparação com outros formatos, ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo DateTime e tipos complexos e semi-estruturados.
* até 70% de compressão.
* indexa a cada 10 000 linhas, que permitem a ignorar linhas.
* uma redução significativa no tempo de execução.

Para ativar o formato ORC, primeiro crie uma tabela com a cláusula *armazenadas como ORC*:

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
   
Em seguida, inserir dados na tabela de ORC da tabela de teste. Por exemplo:

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
   
Pode ler mais sobre o formato ORC no [manual de linguagem de ramo de registo do Apache](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vetorização

Vetorização permite que o Hive processar um lote de linhas de 1024 em conjunto em vez de processar uma linha por vez. Isso significa que operações simples são depressa porque necessita de menos código interno para ser executada.

Para ativar a vetorização prefixo sua consulta do Hive com a definição seguinte:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Para obter mais informações, consulte [Vetorizadas a execução da consulta](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Outros métodos de otimização
Existem mais métodos de otimização que pode considerar a, por exemplo:

* **Hive bucketing:** uma técnica que permite ao cluster ou segmentar grandes conjuntos de dados para otimizar o desempenho de consulta.
* **Otimização da junção:** otimização de execução da consulta do Hive a planear melhorar a eficiência de associações e reduzir a necessidade de sugestões de utilizador. Para obter mais informações, consulte [Otimização da junção](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumentar a redutores**.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a vários métodos de otimização de consulta do Hive comuns. Para obter mais informações, consulte os artigos seguintes:

* [Utilizar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Analisar dados de atraso de voo com o Apache Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analisar dados do Twitter com o Apache Hive no HDInsight](hdinsight-analyze-twitter-data.md)
* [Analisar dados do sensor utilizando a consola de consultas do Apache Hive no Apache Hadoop no HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Utilizar o Apache Hive com HDInsight para analisar os registos de Web sites](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
