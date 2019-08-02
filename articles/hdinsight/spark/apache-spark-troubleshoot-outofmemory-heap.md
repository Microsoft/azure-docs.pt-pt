---
title: Exceção OutOfMemoryError para Apache Spark no Azure HDInsight
description: Exceção OutOfMemoryError para Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620990"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Cenário: Exceção OutOfMemoryError para Apache Spark no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Apache Spark falhou com uma exceção sem tratamento OutOfMemoryError. Você pode receber uma mensagem de erro semelhante a:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>Causa

A causa mais provável dessa exceção não é memória de heap suficiente. Seu aplicativo Spark requer memória de heap de JVM (máquinas virtuais Java) suficiente ao executar como executores ou drivers.

## <a name="resolution"></a>Resolução

1. Determine o tamanho máximo dos dados que a aplicação Spark vai processar. Faça uma estimativa do tamanho com base no máximo do tamanho dos dados de entrada, os dados intermediários produzidos transformando os dados de entrada e os dados de saída produzidos mais transformando os dados intermediários. Se a estimativa inicial não for suficiente, aumente o tamanho ligeiramente e itere até que os erros de memória sejam sublados.

1. Confirme que o cluster do HDInsight a ser utilizado tem recursos suficientes em termos de memória, assim como núcleos, para alojar a aplicação Spark. Isto pode ser determinado na secção Métricas do Cluster da IU do YARN do cluster, ao ver os valores de Memória Utilizada vs. Memória Total e VCores Utilizados vs. Total de VCores.

    ![exibição de memória do yarn Core](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Defina as seguintes configurações do Spark com os valores apropriados. Equilibre os requisitos do aplicativo com os recursos disponíveis no cluster. Esses valores não devem exceder 90% da memória e dos núcleos disponíveis, conforme exibido pelo YARN, e também devem atender ao requisito mínimo de memória do aplicativo Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memória total usada por todos os executores =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memória total usada pelo driver =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória do Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* Depurando o [aplicativo Spark em clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
