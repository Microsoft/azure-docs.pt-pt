---
title: Resolução de problemas Apache Spark em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com Apache Spark e Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: b54b9d932505ada890ac21c1b8de3178ad2f0042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867513"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Resolver problemas relacionados com o Apache Spark com o Azure HDInsight

Conheça as principais questões e suas resoluções ao trabalhar com cargas Apache Spark em [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como configuro uma aplicação Apache Spark através do Apache Ambari em clusters?

Os valores de configuração da faísca podem ser afinados ajuda a evitar uma exceção à aplicação Apache `OutofMemoryError` Spark. Os seguintes passos mostram valores de configuração de faíscas predefinidos no Azure HDInsight:

1. Faça login em Ambari `https://CLUSTERNAME.azurehdidnsight.net` com as suas credenciais de cluster. O ecrã inicial apresenta um painel de visão geral. Existem ligeiras diferenças cosméticas entre HDInsight 3.6 e 4.0.

1. Navegue até **Spark2**  >  **Configs.**

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Selecione o separador Configs" border="true":::

1. Na lista de configurações, selecione e expanda **as padrão personalizados-spark2**.

1. Procure a definição de valor que precisa de ajustar, como **spark.executor.memory**. Neste caso, o valor de **9728m** é muito elevado.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Selecione personalizado-spark-defaults" border="true":::

1. Defina o valor para a definição recomendada. O valor **de 2048m** é recomendado para esta definição.

1. Guarde o valor e, em seguida, guarde a configuração. Selecione **Guardar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Alterar valor para 2048m" border="true":::

    Escreva uma nota sobre as alterações de configuração e, em seguida, **selecione Guardar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Insira uma nota sobre as alterações que fez" border="true":::

    É notificado se alguma configuração precisar de atenção. Note os itens e, em seguida, **selecione Proceder de qualquer forma**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Selecione Proceder De qualquer forma" border="true":::

1. Sempre que uma configuração é guardada, é-lhe pedido que reinicie o serviço. **Selecione Reiniciar**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Selecione reiniciar" border="true":::

    Confirme o recomeço.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Selecione Confirmar Reiniciar Tudo" border="true":::

    Pode rever os processos que estão a decorrer.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Rever processos de execução" border="true":::

1. Pode adicionar configurações. Na lista de configurações, selecione **Custom-spark2-defaults** e, em seguida, **selecione Add Property**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Selecione adicionar propriedade" border="true":::

1. Defina uma nova propriedade. Pode definir uma única propriedade utilizando uma caixa de diálogo para configurações específicas, como o tipo de dados. Ou, pode definir várias propriedades usando uma definição por linha.

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de **4g**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Definir novo imóvel" border="true":::

1. Guarde a configuração e, em seguida, reinicie o serviço como descrito nos passos 6 e 7.

Estas alterações são em todo o agrupamento, mas podem ser ultrapassadas quando se submete o trabalho de Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como posso configurar uma aplicação Apache Spark usando um Caderno Jupyter em clusters?

Na primeira célula do Caderno Jupyter, após a diretiva **%%configure,** especifique as configurações Spark em formato JSON válido. Alterar os valores reais conforme necessário:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Adicionar uma configuração" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como configuro uma aplicação Apache Spark através do Apache Livy em clusters?

Submeta o pedido Spark à Livy utilizando um cliente REST como o cURL. Utilize um comando semelhante ao seguinte. Alterar os valores reais conforme necessário:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como configuro uma aplicação Apache Spark através de spark-submit em clusters?

Lançar a concha de faísca utilizando um comando semelhante ao seguinte. Alterar o valor real das configurações se necessário:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Leitura adicional

[Submissão de emprego da Apache Spark em clusters HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* [Visão geral da gestão da memória de faíscas](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depuração da aplicação Spark em clusters HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).