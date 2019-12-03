---
title: Corrigir um erro do hive sem memória no Azure HDInsight
description: Corrigir um erro Hive sem memória no HDInsight. O cenário do cliente é uma consulta em muitas tabelas grandes.
keywords: erro de memória insuficiente, OOM, configurações do hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687771"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrigir um erro de Apache Hive memória insuficiente no Azure HDInsight

Saiba como corrigir Apache Hive um erro de OOM (memória insuficiente) ao processar tabelas grandes definindo as configurações de memória do hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Executar Apache Hive consulta em tabelas grandes

Um cliente executou uma consulta de Hive:

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

* T1 é um alias para uma tabela grande, TABLE1, que tem muitos tipos de coluna de cadeia de caracteres.
* Outras tabelas não são tão grandes, mas têm muitas colunas.
* Todas as tabelas estão se unindo, em alguns casos com várias colunas na TABELA1 e em outras.

A consulta do hive levou 26 minutos para ser concluída em um cluster HDInsight de 24 nós a3. O cliente observou as seguintes mensagens de aviso:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Usando o mecanismo de execução de Apache Tez. A mesma consulta foi executada por 15 minutos e, em seguida, gerou o seguinte erro:

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

O erro permanece ao usar uma máquina virtual maior (por exemplo, D12).

## <a name="debug-the-out-of-memory-error"></a>Depurar o erro de memória insuficiente

Nossas equipes de suporte e engenharia em conjunto encontraram um dos problemas que causam o erro de memória insuficiente foi um [problema conhecido descrito no Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

"Quando Hive. auto. converter. Join. noconditionaltask = true, verificamos noconditionaltask. size e, se a soma dos tamanhos das tabelas na junção do mapa for menor que noconditionaltask. Size o plano geraria uma junção de mapa, o problema com isso é que o cálculo não é necessário em conta a sobrecarga introduzida por uma implementação de tabela de hash diferente como resultado se a soma dos tamanhos de entrada for menor do que o tamanho do noconditionaltask por uma pequena margem, as consultas entrarão em OOM. "

O **Hive. auto. converter. Join. noconditionaltask** no arquivo hive-site. xml foi definido como **true**:

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

É provável que a junção de mapa tenha sido a causa do erro de memória insuficiente no espaço de heap do Java. Conforme explicado na postagem do blog [configurações de memória do Hadoop yarn no HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando o mecanismo de execução do tez é usado, o espaço de heap usado realmente pertence ao contêiner tez. Consulte a imagem a seguir descrevendo a memória do contêiner tez.

![Diagrama de memória de contêiner tez: erro de memória insuficiente do hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugere a postagem do blog, as duas configurações de memória a seguir definem a memória do contêiner para o heap: **Hive. tez. Container. Size** e **Hive. tez. java.** . Da nossa experiência, a exceção de memória insuficiente não significa que o tamanho do contêiner é muito pequeno. Isso significa que o tamanho do heap do Java (Hive. tez. java. bardelers) é muito pequeno. Assim, sempre que você vir memória insuficiente, poderá tentar aumentar **Hive. tez. java.** Primes. Se necessário, talvez seja necessário aumentar **Hive. tez. Container. Size**. A 80 configuração **Java.** @ @ @ @ 1% do **contêiner. Size**.

> [!NOTE]  
> A configuração **Hive. tez. java.** @ @ @ deve ser menor do que **Hive. tez. Container. Size**.

Como uma máquina D12 tem 28 GB de memória, decidimos usar um tamanho de contêiner de 10 GB (10240 MB) e atribuir 80% a Java. aceita:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Com as novas configurações, a consulta foi executada com êxito em menos de 10 minutos.

## <a name="next-steps"></a>Passos seguintes

Obter um erro de OOM não significa necessariamente que o tamanho do contêiner seja muito pequeno. Em vez disso, você deve definir as configurações de memória para que o tamanho do heap seja aumentado e seja pelo menos 80% do tamanho da memória do contêiner. Para otimizar consultas de Hive, consulte [otimizar Apache Hive consultas para Apache Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md).
