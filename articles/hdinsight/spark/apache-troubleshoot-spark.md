---
title: Resolver problemas relacionados com o Spark no HDInsight do Azure
description: Obtenha respostas a perguntas comuns sobre como trabalhar com o Apache Spark e o Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: aad35aa7a958e8bdaf1479d1ffbbad5bf213d46a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339249"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Spark com o Azure HDInsight

Saiba mais sobre os principais problemas e resolução ao trabalhar com [Apache Spark](https://spark.apache.org/) payloads na [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark com o Apache Ambari em clusters?

### <a name="resolution-steps"></a>Passos de resolução

Valores de configuração de Spark podem ser ajustados ajudar a evitar uma exceção de OutofMemoryError de aplicação do Apache Spark. Os passos seguintes mostram padrão valores de configuração do Spark no Azure HDInsight: 

1. Na lista de clusters, selecione **Spark2**.

    ![Selecione o cluster a partir da lista](./media/apache-troubleshoot-spark/update-config-1.png)

2. Selecione o **configurações** separador.

    ![Selecione o separador de configurações](./media/apache-troubleshoot-spark/update-config-2.png)

3. Na lista de configurações, selecione **personalizada-spark2-predefinições**.

    ![Selecionar as predefinições de spark personalizado](./media/apache-troubleshoot-spark/update-config-3.png)

4. Procure a definição do valor que precisa para ajustar, tal como **spark.executor.memory**. Neste caso, o valor de **4608m** é demasiado elevada.

    ![Selecione o campo de spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Defina o valor para a definição recomendada. O valor **2048m** é recomendada para esta definição.

    ![Altere o valor para 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Guarde o valor e, em seguida, guarde a configuração. Na barra de ferramentas, selecione **guardar**.

    ![Guardar a definição e configuração](./media/apache-troubleshoot-spark/update-config-6a.png)

    Será notificado se quaisquer configurações que necessitam de atenção. Tenha em atenção os itens e, em seguida, selecione **continuar mesmo assim**. 

    ![Selecione continuar mesmo assim](./media/apache-troubleshoot-spark/update-config-6b.png)

    Escrever uma nota sobre as alterações de configuração e, em seguida, selecione **guardar**.

    ![Introduza uma nota sobre as alterações que efetuou](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Sempre que uma configuração é guardada, lhe for pedido para reiniciar o serviço. Selecione **reiniciar**.

    ![Selecione o reinício](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme o reinício.

    ![Selecionar confirmar reinicie todos](./media/apache-troubleshoot-spark/update-config-7b.png)

    Pode rever os processos que estão em execução.

    ![Rever os processos em execução](./media/apache-troubleshoot-spark/update-config-7c.png)

8. É possível adicionar configurações. Na lista de configurações, selecione **personalizada-spark2-predefinições**e, em seguida, selecione **Adicionar propriedade**.

    ![Selecione Adicionar propriedade](./media/apache-troubleshoot-spark/update-config-8.png)

9. Defina uma nova propriedade. Pode definir uma propriedade de única através de uma caixa de diálogo de definições específicas, como o tipo de dados. Em alternativa, pode definir várias propriedades, utilizando uma definição por linha. 

    Neste exemplo, o **spark.driver.memory** propriedade é definida com um valor de **4g**.

    ![Definir a nova propriedade](./media/apache-troubleshoot-spark/update-config-9.png)

10. Guardar a configuração e, em seguida, reinicie o serviço, conforme descrito nos passos 6 e 7.

Estas alterações estão em todo o cluster, mas podem ser substituídas ao submeter a tarefa do Spark.

### <a name="additional-reading"></a>Leitura adicional

[Submissão de tarefas do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark através de um bloco de notas do Jupyter nos clusters?

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual Spark configurações tem de ser definida e para quais os valores, veja o que faz com que uma exceção de OutofMemoryError de aplicação do Apache Spark.

2. A primeira célula do bloco de notas Jupyter, depois do **% % configurar** diretiva, especifique as configurações do Spark no formato JSON válido. Altere os valores reais conforme necessário:

    ![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Leitura adicional

[Submissão de tarefas do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark com o Apache Livy em clusters?

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual Spark configurações tem de ser definida e para quais os valores, veja o que faz com que uma exceção de OutofMemoryError de aplicação do Apache Spark. 

2. Utilizar um cliente REST, como o cURL para submeter a aplicação Spark Livy. Utilize um comando semelhante ao seguinte. Altere os valores reais conforme necessário:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Leitura adicional

[Submissão de tarefas do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como posso configurar uma aplicação com o spark-submit do Apache Spark em clusters?

### <a name="resolution-steps"></a>Passos de resolução

1. Para determinar qual Spark configurações tem de ser definida e para quais os valores, veja o que faz com que uma exceção de OutofMemoryError de aplicação do Apache Spark.

2. Inicie a shell do spark com um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Leitura adicional

[Submissão de tarefas do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>O que faz com que uma exceção de OutofMemoryError de aplicação do Apache Spark?

### <a name="detailed-description"></a>Descrição detalhada

A aplicação Spark falha, com os seguintes tipos de exceções não identificadas:

```apache
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

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

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

### <a name="probable-cause"></a>Causa provável

A causa mais provável desta exceção é que não suficiente memória de heap é alocada para as máquinas de virtuais de Java (JVMs). Estes JVMs iniciados como executor ou controladores como parte da aplicação Spark. 

### <a name="resolution-steps"></a>Passos de resolução

1. Determinar o tamanho máximo dos dados do Spark identificadores de aplicativo. Pode fazer uma estimativa com base no tamanho máximo dos dados de entrada, os dados intermediários, que são produzidos por transformar os dados de entrada e os dados de saída são produzidos quando o aplicativo está ainda mais a transformar os dados intermediários. Esse processo pode ser um iterativo, se não pode fazer uma estimativa inicial de formal. 

2. Certifique-se de que o cluster do HDInsight que pretende utilizar tem recursos suficientes em termos de memória e núcleos para acomodar a aplicação Spark. Pode realizar esta tarefa, ver a secção de métricas de cluster da IU do YARN para os valores dos **memória utilizada** vs. **Total de memória**, e **VCores utilizados** vs. **Total de VCores**.

3. Defina as seguintes configurações do Spark com valores apropriados, que não devem ter mais de 90% da memória disponível e núcleos. Os valores devem ser bem dentro os requisitos de memória da aplicação Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Para calcular o total de memória utilizada por todos os executores: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Para calcular o total de memória usada pelo driver:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Leitura adicional

- [Descrição geral da gestão de memória de Apache Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depurar um aplicativo do Apache Spark num cluster do HDInsight](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Consultar Também
[Resolver problemas com o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
