---
title: Use SparkCruise em Azure HDInsight para acelerar as consultas de Apache Spark
description: Saiba como usar a plataforma de otimização SparkCruise para melhorar a eficiência das consultas de Apache Spark.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 9b68af13aef694e1ae457fd1d0f07b8e9bffb61b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068171"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise em Azure HDInsight

Este documento discute a funcionalidade Azure HDInsight *SparkCruise,* que reutiliza automaticamente os cálculos apache Spark para aumentar a eficiência da consulta.

## <a name="overview"></a>Descrição Geral

As consultas que você executou numa plataforma de análise como Apache Spark, estão decompostas num plano de consulta que contém subqueries menores. Estas subqueries podem aparecer repetidamente através de planos de consulta para múltiplas consultas. Cada vez que ocorrem, são re-executados para devolver os resultados. No entanto, a reexame da mesma consulta pode ser ineficiente e levar a custos de computação desnecessários.

*O SparkCruise* é uma plataforma de otimização de carga de trabalho que pode reutilizar cálculos comuns, diminuindo o tempo de execução geral da consulta e os custos de transferência de dados. A plataforma utiliza o conceito de uma *vista materializada*, que é uma consulta cujos resultados são armazenados em forma pré-computorizada. Estes resultados podem então ser reutilizados quando a própria consulta aparecer mais tarde, em vez de recomporem os resultados novamente.

## <a name="setup-and-installation"></a>Configuração e instalação

O SparkCruise está disponível em todos os clusters HDInsight 4.0 com Spark 2.3 ou 2.4. Os ficheiros da biblioteca SparkCruise estão instalados `/opt/peregrine/` no diretório do seu cluster HDInsight. Para funcionar corretamente, *o SparkCruise* requer as seguintes propriedades de configuração, que são definidas por padrão.

* `spark.sql.queryExecutionListeners` está definido para `com.microsoft.peregrine.spark.listeners.PlanLogListener` , que permite registar os planos de consulta.
* `spark.sql.extensions` está definido para `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , que permite as regras optimizantes para a materialização e reutilização on-line.

## <a name="computation-reuse-in-spark-sql"></a>Reutilização de Computação em Spark SQL

O seguinte cenário de amostra ilustra como usar *o SparkCruise* para otimizar as consultas de Faíscas Apache. 

1. SSH no nó da cabeça do seu aglomerado de faíscas.
1. Escreva `spark-shell`.
1. Executa o seguinte corte de código, que executa algumas consultas básicas usando dados de amostra no cluster.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Utilize a ferramenta de análise de consulta *SparkCruise* para analisar os planos de consulta das consultas anteriores, que são armazenados nos registos de aplicações Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Veja a saída do processo de análise, que é um ficheiro de feedback. Este ficheiro de feedback contém anotações para futuras consultas spark SQL. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

O `analyze` comando analisa os planos de consulta e cria uma representação tabular da carga de trabalho. Esta tabela de carga de trabalho pode ser consultada usando o caderno *WorkloadInsights* incluído no repositório [de amostras de sparkcruise HDInsight.](https://github.com/Azure-Samples/azure-sparkcruise-samples) Em seguida, o `views` comando identifica expressões de subplano comuns e seleciona expressões de subplano interessantes para futuras materialização e reutilização. A saída é um ficheiro de feedback que contém anotações para futuras consultas spark SQL. 

O `show` comando apresenta uma saída como o seguinte texto:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

O ficheiro de feedback contém entradas no seguinte formato: `subplan-identifier [Materialize|Reuse] input/path/to/action` . Neste exemplo, há duas assinaturas únicas: uma representando as duas primeiras consultas repetidas e a segunda representando o predicado filtro nas duas últimas consultas. Com este ficheiro de feedback, as seguintes consultas quando submetidas novamente irão agora materializar e reutilizar automaticamente os subplanos comuns. 

Para testar as otimizações, execute outro conjunto de consultas de amostra.

1. Escreva `spark-shell`.
1. Executar o seguinte corte de código

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Volte a ver o ficheiro de comentários, para ver as assinaturas das consultas que foram reutilizadas.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

O `show` comando dá uma saída semelhante ao seguinte texto:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Embora o valor literal na consulta tenha mudado de `'11%'` `'12%'` para, *o SparkCruise* ainda pode combinar consultas anteriores com novas consultas com ligeiras variações como a evolução dos valores literais e versões de conjunto de dados. Se houver alterações importantes numa consulta, pode refazer a ferramenta de análise para identificar novas consultas que possam ser reutilizadas.

Nos bastidores, o *SparkCruise* desencadeia um subquery para materializar o subplano selecionado a partir da primeira consulta que o contém. Consultas posteriores podem ler diretamente os subplanos materializados em vez de recomulhá-los. Nesta carga de trabalho, os subplanos serão materializados de forma online pela primeira e terceira consultas. Podemos ver a mudança de plano de consultas após a concretização dos subplanos comuns.

Pode ver que existem agora quatro novas subexpressões materializadas disponíveis para serem reutilizadas em consultas posteriores.

## <a name="clean-up"></a>Limpeza

Os ficheiros de feedback, os subplanos materializados e os registos de consultas são persistidos nas sessões de Spark. Para remover estes ficheiros, executar o seguinte comando:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Passos seguintes

* [Utilize o caderno de insights de trabalho para determinar os benefícios da SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando a cache IO IO do Azure HDInsight](apache-spark-improve-performance-iocache.md)
* [Otimizar os empregos da Apache Spark em HDInsight](./apache-spark-perf.md)
* [SparkCruise: Reutilização de Computação Mãos Livres em Faísca](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Diretrizes apache spark em Azure HDInsight](./spark-best-practices.md)
