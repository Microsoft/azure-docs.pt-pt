---
title: Gerenciar recursos para o cluster Apache Spark no Azure HDInsight
description: Saiba como gerenciar recursos para clusters Spark no Azure HDInsight para melhorar o desempenho.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 8c4306b9fb7a6f45ccad45083b6efc019e9a2920
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951675"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerenciar recursos para o cluster Apache Spark no Azure HDInsight

Saiba como acessar as interfaces como a interface do usuário do [Apache Ambari](https://ambari.apache.org/) , a interface do usuário do [amApache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e o [servidor de histórico do Spark](./apache-azure-spark-history-server.md) associado ao cluster [Apache Spark](https://spark.apache.org/) e como ajustar a configuração do cluster para obter um desempenho ideal.

## <a name="open-the-spark-history-server"></a>Abrir o servidor de histórico do Spark

O servidor de histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. É uma extensão da interface do usuário da Web do Spark. Para obter informações completas, consulte [servidor de histórico do Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Abrir a interface do usuário do amYarn

Você pode usar a interface do usuário do amYARN para monitorar aplicativos que estão sendo executados no cluster do Spark.

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Em **painéis de cluster**, selecione **yarn**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Iniciar interface do usuário do YARN](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Como alternativa, você também pode iniciar a interface do usuário do amYARN da interface do usuário do amAmbari. Na interface do usuário do amAmbari, navegue até **YARN** >  **links rápidos** > a **interface do usuário do Active > Resource Manager**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicativos Spark

Os três parâmetros principais que podem ser usados para a configuração do Spark dependendo dos requisitos do aplicativo são `spark.executor.instances`, `spark.executor.cores`e `spark.executor.memory`. Um executor é um processo iniciado para um aplicativo Spark. Ele é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos de executor para cada cluster é calculado com base no número de nós de trabalho e no tamanho do nó de trabalho. Essas informações são armazenadas em `spark-defaults.conf` nos nós de cabeçalho do cluster.

Os três parâmetros de configuração podem ser configurados no nível do cluster (para todos os aplicativos que são executados no cluster) ou também podem ser especificados para cada aplicativo individual.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando a interface do usuário do amAmbari

1. Na interface do usuário do amAmbari, navegue até **Spark2** > **configs** > **Personalizar Spark2-padrões**.

    ![Definir parâmetros usando Ambari personalizado](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Definir parâmetros usando Ambari personalizado")

1. Os valores padrão são bons para que quatro aplicativos Spark sejam executados simultaneamente no cluster. Você pode alterar esses valores da interface do usuário, conforme mostrado na seguinte captura de tela:

    ![Definir parâmetros usando Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Definir parâmetros usando Ambari")

1. Selecione **salvar** para salvar as alterações de configuração. Na parte superior da página, você será solicitado a reiniciar todos os serviços afetados. Selecione **reiniciar**.

    ![Reinicializar serviços](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros de um aplicativo em execução no Jupyter Notebook

Para aplicativos em execução no notebook Jupyter, você pode usar a `%%configure` mágica para fazer as alterações de configuração. Idealmente, você deve fazer essas alterações no início do aplicativo antes de executar sua primeira célula de código. Isso garante que a configuração seja aplicada à sessão Livy quando for criada. Se você quiser alterar a configuração em um estágio posterior no aplicativo, deverá usar o parâmetro `-f`. No entanto, ao fazer isso, todo o progresso no aplicativo é perdido.

O trecho a seguir mostra como alterar a configuração de um aplicativo em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Os parâmetros de configuração devem ser passados como uma cadeia de caracteres JSON e devem estar na próxima linha após a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros de um aplicativo enviado usando Spark-Submit

O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo do lote que é enviado usando `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros de um aplicativo enviado usando a ondulação

O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo do lote que é enviado usando a ondulação.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Alterar esses parâmetros em um servidor Spark thrift

O servidor Spark Thrift fornece acesso JDBC/ODBC a um cluster Spark e é usado para atender a consultas SQL do Spark. Ferramentas como Power BI, tableau e assim por diante, usam o protocolo ODBC para se comunicar com o servidor Spark Thrift para executar consultas SQL do Spark como um aplicativo Spark. Quando um cluster Spark é criado, duas instâncias do servidor Spark Thrift são iniciadas, uma em cada nó de cabeçalho. Cada servidor Spark Thrift é visível como um aplicativo Spark na interface do usuário do amYARN.

O servidor Spark Thrift usa a alocação de executor dinâmico do Spark e, portanto, o `spark.executor.instances` não é usado. Em vez disso, o servidor Thrift do Spark usa `spark.dynamicAllocation.maxExecutors` e `spark.dynamicAllocation.minExecutors` para especificar a contagem de executores. Os parâmetros de configuração `spark.executor.cores`e `spark.executor.memory` são usados para modificar o tamanho do executor. Você pode alterar esses parâmetros, conforme mostrado nas seguintes etapas:

* Expanda a categoria **avançado spark2-Thrift-sparkconf** para atualizar os parâmetros `spark.dynamicAllocation.maxExecutors`e `spark.dynamicAllocation.minExecutors`.

    ![Configurar o servidor Thrift do Spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configurar o servidor Thrift do Spark")

* Expanda a categoria **personalizado spark2-Thrift-sparkconf** para atualizar os parâmetros `spark.executor.cores`e `spark.executor.memory`.

    ![Configurar o parâmetro de servidor Spark thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configurar o parâmetro de servidor Spark thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Alterar a memória do driver do servidor Spark thrift

A memória do driver do servidor Spark Thrift está configurada para 25% do tamanho da RAM do nó de cabeçalho, desde que o tamanho total da RAM do nó de cabeçalho seja maior que 14 GB. Você pode usar a interface do usuário do amAmbari para alterar a configuração da memória do driver, conforme mostrado na seguinte captura de tela:

Na interface do usuário do amAmbari, navegue até **Spark2** > **configurações** > **Advanced Spark2-env**. Em seguida, forneça o valor para **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos do cluster Spark

Devido à alocação dinâmica do Spark, os únicos recursos consumidos pelo servidor Thrift são os recursos para os dois mestres de aplicativo. Para recuperar esses recursos, você deve interromper os serviços do servidor Thrift em execução no cluster.

1. Na interface do usuário do amAmbari, no painel esquerdo, selecione **Spark2**.

2. Na página seguinte, selecione **Spark2 Thrift Servers**.

    ![Reiniciar o Thrift Server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Reiniciar o Thrift Server1")

3. Você deve ver as duas cabeçalho em que o Spark2 Thrift Server está em execução. Selecione um dos cabeçalho.

    ![Reiniciar o Thrift Servidor2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Reiniciar o Thrift Servidor2")

4. A próxima página lista todos os serviços em execução naquele cabeçalho. Na lista, selecione o botão suspenso ao lado de Spark2 Thrift Server e, em seguida, selecione **parar**.

    ![Reiniciar Thrift Server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Reiniciar Thrift Server3")
5. Repita essas etapas também no outro cabeçalho.

## <a name="restart-the-jupyter-service"></a>Reiniciar o serviço Jupyter

Inicie a interface do usuário da Web do amAmbari, conforme mostrado no início do artigo. No painel de navegação esquerdo, selecione **Jupyter**, selecione **ações de serviço**e, em seguida, selecione **reiniciar tudo**. Isso inicia o serviço Jupyter em todos os cabeçalho.

![Reiniciar Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Reinicie o Jupyter")

## <a name="monitor-resources"></a>Monitorizar recursos

Inicie a interface do usuário do amYarn conforme mostrado no início do artigo. Na tabela de métricas de cluster na parte superior da tela, verifique os valores das colunas **memória usada** e **total de memória** . Se os dois valores estiverem próximos, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica às colunas **VCores usadas** e **VCores total** . Além disso, na exibição principal, se houver um aplicativo permaneceu no estado **aceito** e não estiver fazendo a transição para o estado **em execução** ou **falha** , isso também pode ser uma indicação de que ele não está obtendo recursos suficientes para iniciar.

![Limite de recursos](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite de recursos")

## <a name="kill-running-applications"></a>Eliminar aplicativos em execução

1. Na interface do usuário do amYarn, no painel esquerdo, selecione **em execução**. Na lista de aplicativos em execução, determine o aplicativo a ser eliminado e selecione a **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Selecione **eliminar aplicativo** no canto superior direito e, em seguida, selecione **OK**.

    ![Eliminar App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Eliminar App2")

## <a name="see-also"></a>Ver também

* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria do Application insights usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores de Apache Spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
