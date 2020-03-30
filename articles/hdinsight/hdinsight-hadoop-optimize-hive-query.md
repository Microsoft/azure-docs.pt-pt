---
title: Otimizar consultas de Hive em Azure HDInsight
description: Este artigo descreve como otimizar as suas consultas apache hive para Hadoop em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 144d51d08a61526ec0f183a63e1fdf5658136293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272334"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimize Apache Hive queries in Azure HDInsight (Otimizar as consultas do Apache Hive no Azure HDInsight)

No Azure HDInsight, existem vários tipos de cluster e tecnologias que podem executar consultas apache hive. Quando criar o seu cluster HDInsight, escolha o tipo de cluster apropriado para ajudar a otimizar o desempenho das suas necessidades de carga de trabalho.

Por exemplo, escolha o tipo de cluster **de consulta interativa** para otimizar para consultas interativas ad hoc. Escolha o tipo de cluster Apache **Hadoop** para otimizar as consultas da Hive usadas como um processo de lote. Os tipos de cluster **de faíscas** e **HBase** também podem executar consultas da Hive. Para obter mais informações sobre a execução de consultas da Hive em vários tipos de cluster HDInsight, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hadoop/hdinsight-use-hive.md)

Os clusters HDInsight do tipo de cluster Hadoop não são otimizados para o desempenho por padrão. Este artigo descreve alguns dos métodos de otimização de desempenho mais comuns da Hive que você pode aplicar às suas consultas.

## <a name="scale-out-worker-nodes"></a>Escalde os nódosos dos trabalhadores

O aumento do número de nós de trabalhador num cluster HDInsight permite que o trabalho aproveite mais mapeadores e redutores a serem executados em paralelo. Existem duas formas de aumentar a escala no HDInsight:

* No momento em que cria um cluster, pode especificar o número de nós dos trabalhadores utilizando o portal Azure, O PowerShell ou interface de linha de comando.  Para obter mais informações, veja [Create HDInsight clusters (Criar clusters do HDInsight)](hdinsight-hadoop-provision-linux-clusters.md). A seguinte imagem mostra a configuração do nó do trabalhador no portal Azure:
  
    ![Nódosos de tamanho de cluster do portal Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Após a criação, também pode editar o número de nós dos trabalhadores para escalar um cluster ainda mais sem recriar um:

    ![Tamanho do cluster de escala do portal Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Para obter mais informações sobre a escala hDInsight, consulte [clusters Scale HDInsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Use Apache Tez em vez de Reduzir o mapa

[Apache Tez](https://tez.apache.org/) é um motor de execução alternativo ao motor MapReduce. Os clusters HDInsight baseados em Linux têm tez ativado por padrão.

![Diagrama de visão geral de HDInsight Apache Tez](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez é mais rápido porque:

* Execute o **gráfico acíclico direcionado (DAG) como um único trabalho no motor MapReduce**. O DAG exige que cada conjunto de mapeadores seja seguido por um conjunto de redutores. Isto faz com que vários mapReduce jobs sejam desviados para cada consulta da Hive. A Tez não tem tal restrição e pode processar o COMPLEXO DAG como um trabalho, minimizando assim a sobrecarga de arranque de emprego.
* **Evita escritos desnecessários.** Vários trabalhos são usados para processar a mesma consulta da Hive no motor MapReduce. A saída de cada trabalho MapReduce é escrita para HDFS para dados intermédios. Uma vez que Tez minimiza o número de empregos para cada consulta da Hive, é capaz de evitar escritos desnecessários.
* **Minimiza os atrasos de arranque.** A Tez é mais capaz de minimizar o atraso de arranque, reduzindo o número de mappers que precisa de iniciar e também melhorando a otimização em toda a parte.
* **Reutiliza os recipientes**. Sempre que possível, a Tez é capaz de reutilizar recipientes para garantir que a latência devido à colocação de recipientes é reduzida.
* **Técnicas de otimização contínua.** Tradicionalmente, a otimização foi feita durante a fase de compilação. No entanto, mais informações sobre as inputs estão disponíveis que permitem uma melhor otimização durante o tempo de execução. A Tez utiliza técnicas de otimização contínua que lhe permitem otimizar ainda mais o plano na fase de tempo de funcionamento.

Para obter mais informações sobre estes conceitos, consulte [Apache TEZ](https://tez.apache.org/).

Você pode fazer qualquer consulta de Hive Tez ativada prefixando a consulta com o seguinte comando definido:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Partição da colmeia

As operações de I/O são o maior estrangulamento de desempenho para executar consultas da Hive. O desempenho pode ser melhorado se a quantidade de dados que precisam de ser lidos puder ser reduzida. Por padrão, as consultas da Hive digitalizam mesas inteiras da Colmeia. No entanto, para consultas que só precisam de digitalizar uma pequena quantidade de dados (por exemplo, consultas com filtragem), este comportamento cria sobrecargas desnecessárias. A partilha da colmeia permite que as consultas da Hive acedam apenas à quantidade necessária de dados nas tabelas da Colmeia.

A partilha da colmeia é implementada reorganizando os dados brutos em novos diretórios. Cada partição tem o seu próprio diretório de ficheiros. A partilha é definida pelo utilizador. O diagrama seguinte ilustra a divisão de uma mesa da Colmeia pela coluna *Ano*. Um novo diretório é criado para cada ano.

![Divisão hDInsight Apache Hive](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Algumas considerações de partição:

* **Não sob partição** - A partilha em colunas com poucos valores pode causar poucas divisórias. Por exemplo, a divisão sobre o género só cria duas divisórias a criar (macho e fêmea), reduzindo assim alver em um máximo de metade.
* **Não sobrepartição** - No outro extremo, criar uma partição numa coluna com um valor único (por exemplo, userid) causa múltiplas divisórias. Sobre a partilha causa muito stress no nó de cluster, uma vez que tem de lidar com o grande número de diretórios.
* **Evite o enviesamento de dados** - Escolha a chave de partição sabiamente para que todas as divisórias sejam uniformes. Por exemplo, a divisão na coluna *do Estado* pode distorcer a distribuição de dados. Uma vez que o estado da Califórnia tem uma população de quase 30x que de Vermont, o tamanho da partição é potencialmente distorcido e o desempenho pode variar tremendamente.

Para criar uma tabela de partição, utilize a *cláusula Partitioned By:*

```sql
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

Uma vez criada a tabela dividida, pode criar divisórias estáticas ou divisórias dinâmicas.

* **A partilha estática** significa que já fragmentou dados nas diretórios apropriadas. Com divisórias estáticas, adicione divisórias da Hive manualmente com base na localização do diretório. O seguinte código é um exemplo.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **A partilha dinâmica** significa que pretende que a Hive crie divisórias automaticamente para si. Uma vez que já criou a tabela de partição a partir da mesa de preparação, tudo o que precisa de fazer é inserir dados na tabela dividida:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Para mais informações, consulte [Tabelas Divididas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Utilize o formato ORCFile

A Hive suporta diferentes formatos de ficheiros. Por exemplo:

* **Texto**: o formato de ficheiro predefinido e funciona com a maioria dos cenários.
* **Avro:** funciona bem para cenários de interoperabilidade.
* **ORC/Parquet:** mais adequado para o desempenho.

O formato ORC (Colunar de Linhas Otimizada) é uma forma altamente eficiente de armazenar dados da Hive. Em comparação com outros formatos, a ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo datatime e tipos complexos e semi-estruturados.
* até 70% de compressão.
* índices a cada 10.000 linhas, que permitem saltar filas.
* uma queda significativa na execução em execução em execução em execução.

Para ativar o formato ORC, cria primeiro uma tabela com a cláusula *Armazenada como ORC:*

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Em seguida, insira os dados na tabela ORC a partir da tabela de preparação. Por exemplo:

```sql
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

Pode ler mais sobre o formato ORC no manual de linguagem da [colmeia Apache](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorização

A vectorização permite que a Colmeia processe um lote de 1024 linhas em vez de processar uma linha de cada vez. Significa que as operações simples são feitas mais rapidamente porque menos código interno precisa de ser executado.

Para permitir a vectorização prefixar a sua consulta da Colmeia com a seguinte definição:

```hive
set hive.vectorized.execution.enabled = true;
```

Para mais informações, consulte a execução de [consulta vetoriada.](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)

## <a name="other-optimization-methods"></a>Outros métodos de otimização

Há mais métodos de otimização que se pode considerar, por exemplo:

* **Balde de colmeia:** uma técnica que permite agrupar ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
* **Junte-se à otimização:** otimização do planeamento de execução de consulta da Hive para melhorar a eficiência das juntas e reduzir a necessidade de dicas do utilizador. Para mais informações, consulte [Integrar a otimização.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)
* **Aumentar redutores**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu vários métodos comuns de otimização de consulta da Hive. Para saber mais, consulte os seguintes artigos:

* [Use a Colmeia Apache no HDInsight](hadoop/hdinsight-use-hive.md)
* [Analise os dados do atraso de voo utilizando a Consulta Interativa no HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Analise os dados do Twitter usando a Hive Apache no HDInsight](hdinsight-analyze-twitter-data-linux.md)
