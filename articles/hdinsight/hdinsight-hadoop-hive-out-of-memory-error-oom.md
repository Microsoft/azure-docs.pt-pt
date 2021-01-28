---
title: Corrija uma Colmeia fora de erro de memória em Azure HDInsight
description: Corrija uma Colmeia com um erro de memória em HDInsight. O cenário do cliente é uma consulta em muitas mesas grandes.
keywords: fora do erro de memória, OOM, configurações de Colmeia
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: c0810d33f3ac939b9382bf321448ed72b6d87474
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945720"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrija uma Colmeia Apache fora de erro de memória em Azure HDInsight

Aprenda a corrigir um erro da Colmeia Apache fora da memória (OOM) ao processar grandes tabelas configurando as definições de memória da Colmeia.

## <a name="run-apache-hive-query-against-large-tables"></a>Executar consulta de Colmeia Apache contra grandes mesas

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
* Todas as mesas estão se juntando, em alguns casos com várias colunas no TABLE1 e outras.

A consulta da Colmeia demorou 26 minutos a terminar num aglomerado de 24 nós A3 HDInsight. O cliente reparou nas seguintes mensagens de aviso:

```output
    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product
```

Usando o motor de execução Apache Tez. A mesma consulta durou 15 minutos, e depois atirou o seguinte erro:

```output
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
```

O erro permanece quando se utiliza uma máquina virtual maior (por exemplo, D12).

## <a name="debug-the-out-of-memory-error"></a>Depurar o erro de memória

As nossas equipas de apoio e engenharia encontraram juntos um dos problemas que causaram o erro de memória foi um [problema conhecido descrito no Apache JIRA:](https://issues.apache.org/jira/browse/HIVE-8306)

"Quando hive.auto.convert.join.noconditionaltask = verdadeiro verificamos não o tamanho do não-especial.e se a soma dos tamanhos das tabelas na junção do mapa for inferior a nãoconditionaltask.size o plano geraria uma junção de mapa, a questão é que o cálculo não tem em conta a sobrecarga introduzida pela implementação de ísselo diferente como resultado se a soma dos tamanhos de entrada for menor do que o tamanho de não-condicionaltask por uma pequena margem consultas atingirá a OOM."

O **ficheiro hive.auto.convert.join.noconditionaltask** no hive-site.xml ficheiro foi definido como **verdadeiro**:

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

É provável que o mapa se juntasse foi a causa do Espaço Java Heap por erro de memória. Como explicado no blog post [Hadoop Yarn definições de memória em HDInsight](/archive/blogs/shanyu/hadoop-yarn-memory-settings-in-hdinsight), quando o motor de execução Tez é usado o espaço de pilha usado realmente pertence ao recipiente Tez. Veja a seguinte imagem descrevendo a memória do recipiente Tez.

![Diagrama de memória do recipiente Tez: Colmeia fora do erro de memória](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugere o post do blog, as duas definições de memória que se seguem definem a memória do recipiente para a pilha: **hive.tez.container.size** e **hive.tez.java.opts**. Pela nossa experiência, a exceção fora da memória não significa que o tamanho do contentor é muito pequeno. Significa que o tamanho da pilha de Java (hive.tez.java.opts) é muito pequeno. Assim, sempre que vir fora da memória, pode tentar aumentar **a colmeia.tez.java.opts**. Se necessário, poderá ter de aumentar **o tamanho da colmeia.tez.container**. A definição **de java.opts** deve ser de cerca de 80% do tamanho do **contentor.**

> [!NOTE]  
> A configuração **do hive.tez.java.opts** deve ser sempre menor do que **o tamanho de hive.tez.container**.

Como uma máquina D12 tem memória de 28 GB, decidimos usar um tamanho de contentor de 10 GB (10240 MB) e atribuir 80% a java.opts:

```console
SET hive.tez.container.size=10240
SET hive.tez.java.opts=-Xmx8192m
```

Com as novas definições, a consulta foi com sucesso em menos de 10 minutos.

## <a name="next-steps"></a>Próximos passos

Obter um erro OOM não significa necessariamente que o tamanho do recipiente é muito pequeno. Em vez disso, deve configurar as definições de memória de modo a que o tamanho da pilha seja aumentado e seja pelo menos 80% do tamanho da memória do recipiente. Para otimizar as consultas de Colmeia, consulte [as consultas de Hive Otimize Apache para Apache Hadoop em HDInsight.](hdinsight-hadoop-optimize-hive-query.md)