---
title: Sufoco de problemas Apache Spark em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com Apache Spark e Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271944"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Spark com o Azure HDInsight

Conheça os principais problemas e as suas resoluções ao trabalhar com as cargas da Apache Spark em [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como configuro uma aplicação Apache Spark através do Apache Ambari em clusters?

Os valores de configuração da `OutofMemoryError` faísca podem ser ajustados ajudando a evitar uma exceção à aplicação Apache Spark. Os seguintes passos mostram valores de configuração padrão da Faísca no Azure HDInsight:

1. Faça login em `https://CLUSTERNAME.azurehdidnsight.net` Ambari com as suas credenciais de cluster. O ecrã inicial apresenta um painel de visão geral. Existem ligeiras diferenças cosméticas entre hDInsight 3.6 e 4.0.

1. Navegue até **Spark2** > **Configs.**

    ![Selecione o separador Configs](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Na lista de configurações, selecione e expanda os **padrãos Custom-spark2**.

1. Procure a definição de valor que precisa de ajustar, como **spark.executor.memory**. Neste caso, o valor de **9728m** é demasiado elevado.

    ![Selecione padrão de faísca personalizada](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Defina o valor para a definição recomendada. O valor **de 2048m** é recomendado para esta definição.

1. Poupe o valor e, em seguida, guarde a configuração. Selecione **Guardar**.

    ![Alterar valor para 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Escreva uma nota sobre as alterações de configuração e, em seguida, selecione **Guardar**.

    ![Insira uma nota sobre as alterações que fez](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    É notificado se alguma configuração necessitar de atenção. Tome nota dos itens e, em seguida, selecione **Proceder de qualquer maneira**.

    ![Selecione Proceder de qualquer maneira](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Sempre que uma configuração é guardada, é-lhe solicitado que reinicie o serviço. Selecione **Reiniciar**.

    ![Selecione reiniciar](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Confirme o recomeço.

    ![Selecione Confirmar Reiniciar tudo](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Pode rever os processos que estão a decorrer.

    ![Rever processos de execução](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Pode adicionar configurações. Na lista de configurações, selecione padrão **seletiva 2- spark2**, e, em seguida, selecione **Adicionar Propriedade**.

    ![Selecione adicionar propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Defina uma nova propriedade. Pode definir uma única propriedade utilizando uma caixa de diálogo para configurações específicas, como o tipo de dados. Ou, pode definir múltiplas propriedades usando uma definição por linha.

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de **4g**.

    ![Definir nova propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Guarde a configuração e, em seguida, reinicie o serviço conforme descrito nos passos 6 e 7.

Estas alterações são em todo o cluster, mas podem ser ultrapassadas quando se submete ao trabalho da Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como configuro uma aplicação Apache Spark através de um bloco de notas Jupyter em clusters?

Na primeira célula do caderno Jupyter, após a diretiva **%%configura,** especifique as configurações spark em formato JSON válido. Alterar os valores reais conforme necessário:

![Adicione uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como configuro uma aplicação Apache Spark através do Apache Livy em clusters?

Envie o pedido Spark à Livy utilizando um cliente REST como cURL. Utilize um comando semelhante ao seguinte. Alterar os valores reais conforme necessário:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como configuro uma aplicação Apache Spark através de spark-submit em clusters?

Lance a casca de faísca utilizando um comando semelhante ao seguinte. Alterar o valor real das configurações conforme necessário:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Leitura adicional

[Submissão de emprego da Apache Spark em clusters HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Visão geral da [gestão da memória de faíscas.](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)

* [Depuração da aplicação Spark em clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
