---
title: Corrija uma Colmeia fora do erro de memória no Azure HDInsight
description: Corrija uma Colmeia a partir de um erro de memória no HDInsight. O cenário do cliente é uma consulta em muitas mesas grandes.
keywords: fora do erro de memória, OOM, Definições da Colmeia
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687771"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrija uma Colmeia Apache fora do erro de memória no Azure HDInsight

Aprenda a corrigir um erro da Hive Apache a partir da memória (OOM) ao processar grandes tabelas configurando as definições de memória da Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Executar consulta da Colmeia Apache contra grandes mesas

Um cliente fez uma consulta da Hive:

```sql
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
```

Algumas nuances desta consulta:

* T1 é um pseudónimo de uma grande mesa, TABLE1, que tem muitos tipos de colunas STRING.
* Outras mesas não são tão grandes, mas têm muitas colunas.
* Todas as mesas estão se juntando umas às outras, em alguns casos com várias colunas em TABLE1 e outros.

A consulta da Hive demorou 26 minutos a terminar num cluster A3 HDInsight de 24 nós. O cliente reparou nas seguintes mensagens de aviso:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Usando o motor de execução Apache Tez. A mesma consulta durou 15 minutos e, em seguida, atirou o seguinte erro:

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

## <a name="debug-the-out-of-memory-error"></a>Depurar o erro de memória

As nossas equipas de apoio e engenharia juntos encontraram um dos problemas que causaram o erro de memória foi uma [questão conhecida descrita no Apache JIRA:](https://issues.apache.org/jira/browse/HIVE-8306)

"Quando a hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum of tables sizes in the map join is less noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation don't take to to do overhead introduceed by different HashTable implementation as results if the sum of input sizes is small than the noconditionaltask size by a small marginries

O ficheiro **hive.auto.convert.join.noconditionaltask** in the hive-site.xml file was definido para **verdadeiro:**

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

É provável que a adesão ao mapa tenha sido a causa do Espaço Monte Java por erro de memória. Como explicado no post de blogue [Hadoop Yarn definições de memória no HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando o motor de execução Tez é usado, o espaço de pilha usado realmente pertence ao recipiente Tez. Veja a seguinte imagem descrevendo a memória do recipiente Tez.

![Diagrama de memória de recipiente tez: Colmeia fora do erro de memória](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugere a publicação de blogue, as duas definições de memória que se seguem definem a memória do recipiente para a pilha: **hive.tez.container.size** e **hive.tez.java.opts**. Pela nossa experiência, a exceção fora da memória não significa que o tamanho do contentor seja muito pequeno. Significa que o tamanho do monte Java (hive.tez.java.opts) é muito pequeno. Assim, sempre que você vê fora da memória, você pode tentar aumentar **hive.tez.java.opts**. Se necessário, poderá ter de aumentar a **colmeia.tez.container.size**. A definição **java.opta** deve ser de cerca de 80% do tamanho do **recipiente.tamanho.**

> [!NOTE]  
> A definição **de hive.tez.java.opts** deve ser sempre menor do que **a hive.tez.container.size**.

Como uma máquina D12 tem memória de 28 GB, decidimos usar um contentor tamanho de 10 GB (10240 MB) e atribuir 80% a java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Com as novas configurações, a consulta correu com sucesso em menos de 10 minutos.

## <a name="next-steps"></a>Passos seguintes

Obter um erro oom não significa necessariamente que o tamanho do recipiente é muito pequeno. Em vez disso, deve configurar as definições de memória de modo a que o tamanho da pilha seja aumentado e seja pelo menos 80% do tamanho da memória do recipiente. Para otimizar consultas de Hive, consulte [Otimize Apache Hive consultas para Apache Hadoop em HDInsight](hdinsight-hadoop-optimize-hive-query.md).
