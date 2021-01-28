---
title: Otimizar consultas de Colmeia em Azure HDInsight
description: Este artigo descreve como otimizar as suas consultas de Hive Apache em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: a15c3e0fb3550c6e50b3fba2279611fdba25bc84
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945568"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Optimize Apache Hive queries in Azure HDInsight (Otimizar as consultas do Apache Hive no Azure HDInsight)

Este artigo descreve algumas das otimizações de desempenho mais comuns que pode usar para melhorar o desempenho das suas consultas de Hive Apache.

## <a name="cluster-type-selection"></a>Seleção do tipo cluster

Em Azure HDInsight, você pode executar consultas apache hive em alguns tipos diferentes de cluster. 

Escolha o tipo de cluster adequado para ajudar a otimizar o desempenho para as suas necessidades de carga de trabalho:

* Escolha o tipo de cluster **de consulta interativa** para otimizar, `ad hoc` consultas interativas. 
* Escolha o tipo de cluster Apache **Hadoop** para otimizar as consultas de Colmeia usadas como um processo de lote. 
* **Os** tipos de cluster Spark e **HBase** também podem executar consultas de Hive, e pode ser apropriado se estiver a executar essas cargas de trabalho. 

Para obter mais informações sobre a execução de consultas de Hive em vários tipos de cluster HDInsight, consulte [o que é a Hive Apache e a HiveQL em Azure HDInsight?](hadoop/hdinsight-use-hive.md)

## <a name="scale-out-worker-nodes"></a>Escalone os nóns dos trabalhadores

Aumentar o número de nós de trabalhadores num cluster HDInsight permite que o trabalho utilize mais mappers e redutores para ser executado em paralelo. Há duas maneiras de aumentar a escala em HDInsight:

* Quando criar um cluster, pode especificar o número de nós de trabalhador utilizando o portal Azure PowerShell ou a interface de linha de comando.  Para obter mais informações, veja [Create HDInsight clusters (Criar clusters do HDInsight)](hdinsight-hadoop-provision-linux-clusters.md). A imagem que se segue mostra a configuração do nó do trabalhador no portal Azure:
  
    ![Nódoas de tamanho do cluster do portal Azure](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Após a criação, pode também editar o número de nós operários para escalar ainda mais um cluster sem recriar um:

    ![Tamanho do cluster de escala de porta azul](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Para obter mais informações sobre o escalonamento hdInsight, consulte [clusters Scale HDInsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Use Apache Tez em vez de Reduzir mapa

[Apache Tez](https://tez.apache.org/) é um motor de execução alternativo ao motor MapReduce. Os clusters HDInsight baseados em Linux têm Tez ativado por padrão.

![Diagrama de visão geral de Apache Tez hdInsight](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez é mais rápido porque:

* **Execute o Gráfico Acíclico Direcionado (DAG) como um único trabalho no motor MapReduce**. O DAG exige que cada conjunto de mappers seja seguido por um conjunto de redutores. Este requisito faz com que vários trabalhos de MapReduce sejam desviados para cada consulta da Colmeia. Tez não tem tal restrição e pode processar o complexo DAG como um trabalho minimizando a sobrecarga de startups de emprego.
* **Evita escritos desnecessários.** Vários trabalhos são usados para processar a mesma consulta de Hive no motor MapReduce. A saída de cada trabalho do MapReduce é escrita para HDFS para dados intermédios. Uma vez que tez minimiza o número de empregos para cada consulta da Colmeia, é capaz de evitar escritos desnecessários.
* **Minimiza os atrasos de arranque**. O Tez é mais capaz de minimizar o atraso no arranque, reduzindo o número de mappers que precisa de iniciar e melhorando também a otimização ao longo de todo.
* **Reutiliza recipientes.** Sempre que possível, a Tez reutilizará os recipientes para garantir que a latência do arranque dos contentores seja reduzida.
* **Técnicas de otimização contínua.** Tradicionalmente, a otimização foi feita durante a fase de compilação. No entanto, mais informações sobre as entradas estão disponíveis que permitem uma melhor otimização durante o tempo de funcionaamento. O Tez utiliza técnicas de otimização contínua que lhe permitem otimizar o plano ainda mais na fase de tempo de funcionaamento.

Para obter mais informações sobre estes conceitos, consulte [Apache TEZ.](https://tez.apache.org/)

Pode fazer qualquer consulta de Hive Tez ativada prefixando a consulta com o seguinte comando definido:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Partição de colmeias

As operações de I/O são o principal estrangulamento de desempenho para executar consultas de Colmeia. O desempenho pode ser melhorado se a quantidade de dados que precisa de ser lido puder ser reduzida. Por padrão, as consultas de Colmeia sondam todas as tabelas da Colmeia. No entanto, para consultas que apenas precisam de digitalizar uma pequena quantidade de dados (por exemplo, consultas com filtragem), este comportamento cria sobrecargas desnecessárias. A partição de colmeias permite que as consultas de Colmeia tenham acesso apenas à quantidade necessária de dados nas tabelas da Colmeia.

A partição de colmeias é implementada reorganizando os dados brutos em novos diretórios. Cada divisória tem o seu próprio diretório de ficheiros. A partição é definida pelo utilizador. O diagrama seguinte ilustra a partição de uma tabela de colmeias pelo *ano* da coluna . Um novo diretório é criado para cada ano.

![Partição de Colmeia Apache HDInsight](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Algumas considerações de partição:

* **Não esteja em partição** - A partilha em colunas com apenas alguns valores pode causar poucas divisórias. Por exemplo, a divisão sobre o género só cria duas divisórias a serem criadas (masculino e feminino), reduzindo assim a latência em um máximo de metade.
* **Não sobre a partição** - Por outro extremo, criar uma divisória numa coluna com um valor único (por exemplo, userid) causa múltiplas divisórias. Sobre a partição causa muito stress no nome do cluster, uma vez que tem que lidar com o grande número de diretórios.
* **Evite distorções de dados** - Escolha a sua chave de partição com sensatez para que todas as divisórias sejam uniformes. Por exemplo, a divisão na coluna *do Estado* pode distorcer a distribuição de dados. Uma vez que o estado da Califórnia tem uma população de quase 30x a de Vermont, o tamanho da partição é potencialmente distorcido e o desempenho pode variar tremendamente.

Para criar uma tabela de divisórias, utilize a *cláusula Partitioned By:*

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

Uma vez criada a mesa partida, pode criar divisórias estáticas ou divisórias dinâmicas.

* **A divisão estática** significa que já tem dados fragmentos nos diretórios apropriados. Com divisórias estáticas, adiciona-se as divisórias hive manualmente com base na localização do diretório. O seguinte corte de código é um exemplo.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **A divisão dinâmica** significa que quer que a Colmeia crie divisórias automaticamente para si. Uma vez que já criou a tabela de divisórias a partir da mesa de preparação, tudo o que precisa fazer é inserir dados na tabela divisória:
  
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

Para mais informações, consulte [As Tabelas Partitioned.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)

## <a name="use-the-orcfile-format"></a>Utilize o formato ORCFile

A Hive suporta diferentes formatos de ficheiros. Por exemplo:

* **Texto**: o formato de ficheiro predefinido e funciona com a maioria dos cenários.
* **Avro**: funciona bem para cenários de interoperabilidade.
* **ORC/Parquet:** mais adequado para o desempenho.

O formato ORC (Coluna de Linha Otimizada) é uma forma altamente eficiente de armazenar dados da Hive. Em comparação com outros formatos, o ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo DateTime e tipos complexos e semi-estruturados.
* até 70% de compressão.
* índices a cada 10.000 linhas, que permitem saltar linhas.
* uma queda significativa na execução em tempo de execução.

Para ativar o formato ORC, primeiro cria uma tabela com a cláusula *Armazenada como ORC:*

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

Em seguida, insira dados na tabela ORC a partir da tabela de preparação. Por exemplo:

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

Pode ler mais sobre o formato ORC no [manual de Linguagem da Colmeia Apache.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)

## <a name="vectorization"></a>Vectorização

Vectorization permite que a Colmeia processe um lote de 1024 linhas em vez de processar uma linha de cada vez. Significa que as operações simples são feitas mais rapidamente porque menos código interno precisa de ser executado.

Para ativar o prefixo de vectorização, a consulta de Hive com a seguinte definição:

```hive
set hive.vectorized.execution.enabled = true;
```

Para obter mais informações, consulte [a execução de consultas vectorizadas.](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)

## <a name="other-optimization-methods"></a>Outros métodos de otimização

Existem mais métodos de otimização que pode considerar, por exemplo:

* **Balde de colmeia:** uma técnica que permite agrupar ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
* **Junte-se à otimização:** otimização do planeamento de execução de consultas da Hive para melhorar a eficiência das juntas e reduzir a necessidade de sugestões do utilizador. Para obter mais informações, consulte [a otimização do Join](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumentar os Redutores**.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu vários métodos comuns de otimização de consulta de Colmeia. Para saber mais, leia os artigos seguintes:

* [Otimizar Apache Hive](./optimize-hive-ambari.md)
* [Analise os dados do atraso de voo utilizando a Consulta Interativa em HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analise os dados do Twitter usando a Hive Apache em HDInsight](hdinsight-analyze-twitter-data-linux.md)
