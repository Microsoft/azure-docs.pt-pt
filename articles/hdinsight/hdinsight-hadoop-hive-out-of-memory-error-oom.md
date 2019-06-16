---
title: Corrigir um ramo de registo fora de erro de memória no Azure HDInsight
description: Corrigi um ramo de registo fora de erro de memória no HDInsight. O cenário de cliente é uma consulta em muitas tabelas grandes.
keywords: sem definições de ramo de registo de erros, OOM, de memória
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2e7328b95aecc8e644d7b9e2ec407a62551fff79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712786"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrigir um Apache Hive fora do erro de memória no Azure HDInsight

Saiba como corrigir um Apache Hive fora do erro de memória (OOM) ao processar tabelas grandes ao configurar as definições de memória do Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Executar consultas do Apache Hive em tabelas grandes

Um cliente executou uma consulta do Hive:

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Algumas nuances desta consulta:

* T1 é um alias para uma grande tabela, TABLE1, com vários tipos de coluna de cadeia de caracteres.
* Outras tabelas são não que a grandes, mas é necessário o número de colunas.
* Todas as tabelas são ingressar em si, em alguns casos com várias colunas na tabela1 e outros.

A consulta do Hive demorou 26 minutos a concluir num nó de cluster de HDInsight A3 24. O cliente observado as seguintes mensagens de aviso:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Por meio do mecanismo de execução do Apache Tez. A mesma consulta for executada durante 15 minutos e, em seguida, apresentou o seguinte erro:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

O erro permanece ao utilizar uma máquina virtual maior (por exemplo, D12).


## <a name="debug-the-out-of-memory-error"></a>Depurar o fora de erro de memória

Nosso suporte e equipas de engenharia em conjunto encontrado um dos problemas fazendo com que o fora de erro de memória foi um [problema descrito no JIRA a Apache conhecido](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesn't take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

O **hive.auto.convert.join.noconditionaltask** hive-site ficheiro foi definido como **verdadeiro**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

É provável que a associação de mapa foi a causa do espaço de área dinâmica para dados Java nosso de erro de memória. Conforme explicado na mensagem de blogue [definições de memória de Yarn do Hadoop no HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando Tez motor de execução é utilizado o heap espaço utilizado, na verdade, pertence ao contentor Tez. Ver a imagem seguinte, que descreve a memória de contentor do Tez.

![Diagrama de memória do Tez contentor: Hive fora do erro de memória](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugere a mensagem de blogue, as seguintes definições de dois memória definem a memória de contentor para o heap: **hive.tez.container.size** e **hive.tez.java.opts**. A nossa experiência, fora de exceção de memória não significa que o tamanho do contentor é demasiado pequeno. Isso significa que o tamanho da pilha de Java (hive.tez.java.opts) é demasiado pequeno. Portanto, sempre que vir fora da memória, pode tentar aumentar **hive.tez.java.opts**. Se for necessário poderá ter de aumentar **hive.tez.container.size**. O **java.opts** definição deve ser cerca de 80% dos **container.size**.

> [!NOTE]  
> A definição **hive.tez.java.opts** tem de ser sempre menor do que **hive.tez.container.size**.
> 
> 

Como uma máquina de D12 tem memória de 28GB, decidimos usar um tamanho de contentor de 10GB (10240MB) e atribuir a 80% para java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Com as novas definições, a consulta foi executado com êxito em menos de 10 minutos.

## <a name="next-steps"></a>Passos Seguintes

Recebendo um erro OOM não significa necessariamente que o tamanho do contentor é demasiado pequeno. Em vez disso, deve configurar as definições de memória para que o tamanho da pilha é aumentado e é, pelo menos, 80% do tamanho de memória do contentor. Para otimizar consultas do Hive, consulte [otimizar Apache Hive consultas para o Apache Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md).
