---
title: Solucionar problemas Apache Spark no Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre como trabalhar com o Apache Spark e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 143a941b8266734a8415c71daafc07681bc13b80
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049598"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Spark com o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções ao trabalhar com cargas de Apache Spark no [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark com o Apache Ambari em clusters?

Os valores de configuração do Spark podem ser ajustados ajudam `OutofMemoryError` a evitar uma exceção Apache Spark aplicativo. As etapas a seguir mostram os valores de configuração padrão do Spark no Azure HDInsight:

1. Faça logon no Ambari `https://CLUSTERNAME.azurehdidnsight.net` com suas credenciais de cluster. A tela inicial exibe um painel de visão geral. Há pequenas diferenças superficiais entre o HDInsight 3,6 e o 4,0.

1. Navegue até**configurações**do **Spark2** > .

    ![Selecione o separador de configurações](./media/apache-troubleshoot-spark/update-config-2.png)

1. Na lista de configurações, selecione e expanda **personalizado-spark2-padrões**.

1. Procure a definição do valor que precisa para ajustar, tal como **spark.executor.memory**. Nesse caso, o valor de **9728m** é muito alto.

    ![Selecionar as predefinições de spark personalizado](./media/apache-troubleshoot-spark/update-config-4.png)

1. Defina o valor para a definição recomendada. O valor **2048m** é recomendada para esta definição.

1. Guarde o valor e, em seguida, guarde a configuração. Selecione **Guardar**.

    ![Altere o valor para 2048m](./media/apache-troubleshoot-spark/update-config-6a.png)

    Escrever uma nota sobre as alterações de configuração e, em seguida, selecione **guardar**.

    ![Introduza uma nota sobre as alterações que efetuou](./media/apache-troubleshoot-spark/update-config-6c.png)

    Será notificado se quaisquer configurações que necessitam de atenção. Tenha em atenção os itens e, em seguida, selecione **continuar mesmo assim**.

    ![Selecione continuar mesmo assim](./media/apache-troubleshoot-spark/update-config-6b.png)

1. Sempre que uma configuração é guardada, lhe for pedido para reiniciar o serviço. Selecione **reiniciar**.

    ![Selecione o reinício](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme o reinício.

    ![Selecionar confirmar reinicie todos](./media/apache-troubleshoot-spark/update-config-7b.png)

    Pode rever os processos que estão em execução.

    ![Rever os processos em execução](./media/apache-troubleshoot-spark/update-config-7c.png)

1. É possível adicionar configurações. Na lista de configurações, selecione **personalizada-spark2-predefinições**e, em seguida, selecione **Adicionar propriedade**.

    ![Selecione Adicionar propriedade](./media/apache-troubleshoot-spark/update-config-8.png)

1. Defina uma nova propriedade. Pode definir uma propriedade de única através de uma caixa de diálogo de definições específicas, como o tipo de dados. Em alternativa, pode definir várias propriedades, utilizando uma definição por linha.

    Neste exemplo, o **spark.driver.memory** propriedade é definida com um valor de **4g**.

    ![Definir a nova propriedade](./media/apache-troubleshoot-spark/update-config-9.png)

1. Guardar a configuração e, em seguida, reinicie o serviço, conforme descrito nos passos 6 e 7.

Estas alterações estão em todo o cluster, mas podem ser substituídas ao submeter a tarefa do Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark através de um bloco de notas do Jupyter nos clusters?

A primeira célula do bloco de notas Jupyter, depois do **% % configurar** diretiva, especifique as configurações do Spark no formato JSON válido. Altere os valores reais conforme necessário:

![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como posso configurar uma aplicação do Apache Spark com o Apache Livy em clusters?

Utilizar um cliente REST, como o cURL para submeter a aplicação Spark Livy. Utilize um comando semelhante ao seguinte. Altere os valores reais conforme necessário:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como posso configurar uma aplicação com o spark-submit do Apache Spark em clusters?

Inicie a shell do spark com um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Leitura adicional

[Submissão de tarefas do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória do Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* Depurando o [aplicativo Spark em clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
