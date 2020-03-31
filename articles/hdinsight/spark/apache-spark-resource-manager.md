---
title: Gerir recursos para o cluster Apache Spark no Azure HDInsight
description: Aprenda a gerir os recursos para clusters Spark no Azure HDInsight para um melhor desempenho.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932096"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerir recursos para o cluster Apache Spark no Azure HDInsight

Saiba como aceder às interfaces como [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI e o [Spark History Server](./apache-azure-spark-history-server.md) associado ao seu cluster [Apache Spark,](https://spark.apache.org/) e como afinar a configuração do cluster para um desempenho ótimo.

## <a name="open-the-spark-history-server"></a>Abra o Servidor de História da Faísca

Spark History Server é o UI web para aplicações Spark concluídas e executantes. É uma extensão da Web UI da Spark. Para obter informações completas, consulte [spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Abra a Yarn UI

Pode utilizar a UI YARN para monitorizar aplicações que estão atualmente em execução no cluster Spark.

1. A partir do [portal Azure,](https://portal.azure.com/)abra o aglomerado de faíscas. Para mais informações, consulte [Lista e mostre clusters.](../hdinsight-administer-use-portal-linux.md#showClusters)

2. A partir de **dashboards cluster,** selecione **Fios**. Quando solicitado, insira as credenciais de administração para o cluster Spark.

    ![Lançamento YARN UI](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Em alternativa, também pode lançar a UI YARN a partir da Ambari UI. A partir do Ambari UI, navegue até **yARN** > **Quick Links** > **Ative** > **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicações Spark

Os três parâmetros-chave que podem ser utilizados para `spark.executor.instances` `spark.executor.cores`a `spark.executor.memory`configuração spark dependendo dos requisitos de aplicação são , e . Um Executor é um processo lançado para uma aplicação Spark. Funciona no nó do trabalhador e é responsável pela execução das tarefas para a aplicação. O número padrão de executores e tamanhos do executor para cada cluster é calculado com base no número de nós dos trabalhadores e no tamanho do nó do trabalhador. Esta informação é `spark-defaults.conf` armazenada nos nós da cabeça do cluster.

Os três parâmetros de configuração podem ser configurados ao nível do cluster (para todas as aplicações que funcionam no cluster) ou podem ser especificados para cada aplicação individual também.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando Ambari UI

1. Desde a ambari UI navegar até **Spark2** > **Configs** > **Spark2-defaults personalizados**.

    ![Definir parâmetros usando o costume Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Definir parâmetros usando o costume Ambari")

1. Os valores predefinidos são bons para ter quatro aplicações Spark executadas simultaneamente no cluster. Pode alterar estes valores a partir da interface do utilizador, como mostra a seguinte imagem:

    ![Definir parâmetros usando Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Definir parâmetros usando Ambari")

1. Selecione **Guardar** para guardar as alterações de configuração. No topo da página, é-lhe pedido que reinicie todos os serviços afetados. Selecione **Reiniciar**.

    ![Serviços de reinício](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros para uma aplicação em execução no caderno Jupyter

Para aplicações em execução no portátil `%%configure` Jupyter, pode usar a magia para fazer as alterações de configuração. Idealmente, deve fazer tais alterações no início da aplicação, antes de executar a sua primeira célula de código. Ao fazê-lo, a configuração é aplicada à sessão Livy, quando é criada. Se pretender alterar a configuração numa fase posterior da `-f` aplicação, deve utilizar o parâmetro. No entanto, ao fazê-lo, todos os progressos na aplicação são perdidos.

O seguinte corte mostra como alterar a configuração para uma aplicação em execução em Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Os parâmetros de configuração devem ser passados como uma corda JSON e devem estar na linha seguinte após a magia, como mostra a coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros para uma aplicação submetida com recurso a spark-submit

O seguinte comando é um exemplo de como alterar os parâmetros `spark-submit`de configuração para uma aplicação de lote que é submetida usando .

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros para uma aplicação submetida com cURL

O comando seguinte é um exemplo de como alterar os parâmetros de configuração para uma aplicação de lote que é submetida usando cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Altere estes parâmetros num Servidor Spark Thrift

O Spark Thrift Server fornece acesso jDBC/ODBC a um cluster Spark e é usado para atender consultas Spark SQL. Ferramentas como Power BI, Tableau, e assim por diante, use o protocolo ODBC para comunicar com o Spark Thrift Server para executar consultas Spark SQL como uma Aplicação spark. Quando um cluster Spark é criado, duas instâncias do Spark Thrift Server são iniciadas, uma em cada nó de cabeça. Cada Spark Thrift Server é visível como uma aplicação Spark no YARN UI.

O Spark Thrift Server utiliza a `spark.executor.instances` atribuição de executor dinâmico spark e, portanto, o não é usado. Em vez disso, `spark.dynamicAllocation.maxExecutors` `spark.dynamicAllocation.minExecutors` o Spark Thrift Server utiliza e especifica a contagem do executor. Os parâmetros `spark.executor.cores`de `spark.executor.memory` configuração, e são usados para modificar o tamanho do executor. Pode alterar estes parâmetros como mostrado nos seguintes passos:

* Expanda a categoria **De spark2-thrift-sparkconf avançado** para atualizar os parâmetros `spark.dynamicAllocation.maxExecutors`, e `spark.dynamicAllocation.minExecutors`.

    ![Configure Spark thrift servidor](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configure Spark thrift servidor")

* Expanda a categoria **Custom spark2-thrift-sparkconf** para atualizar os parâmetros `spark.executor.cores`e `spark.executor.memory`.

    ![Configure Parâmetro de servidor de thrift Spark](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configure Parâmetro de servidor de thrift Spark")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Mude a memória do condutor do Spark Thrift Server

A memória do controlador Spark Thrift Server está configurada para 25% do tamanho de RAM do nó da cabeça, desde que o tamanho total de RAM do nó da cabeça seja superior a 14 GB. Pode utilizar o UI Ambari para alterar a configuração da memória do condutor, como mostra a seguinte imagem:

Do Ambari UI, navegue até **Spark2** > **Configs** > **Advanced spark2-env**. Em seguida, forneça o valor para **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperar os recursos do cluster Spark

Devido à alocação dinâmica spark, os únicos recursos que são consumidos pelo servidor thrift são os recursos para os dois mestres de aplicações. Para recuperar estes recursos, tem de parar os serviços thrift Server em funcionamento no cluster.

1. A partir do Ambari UI, do painel esquerdo, selecione **Spark2**.

2. Na página seguinte, selecione **Spark2 Thrift Servers**.

    ![Reiniciar o servidor thrift1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Reiniciar o servidor thrift1")

3. Devias ver os dois nódosos em que o Spark2 Thrift Server está a funcionar. Selecione um dos cabeçados.

    ![Reiniciar o servidor thrift2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Reiniciar o servidor thrift2")

4. A página seguinte lista todos os serviços em execução no nó de cabeceira. A partir da lista, selecione o botão drop-down ao lado do Spark2 Thrift Server e, em seguida, selecione **Stop**.

    ![Reiniciar o servidor thrift3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Reiniciar o servidor thrift3")
5. Repita estes passos no outro nódoo também.

## <a name="restart-the-jupyter-service"></a>Reinicie o serviço Jupyter

Lance a Ambari Web UI como mostrado no início do artigo. A partir do painel de navegação esquerdo, **selecione Jupyter**, selecione Ações de **Serviço,** e, em seguida, selecione **Reiniciar Tudo**. Isto começa o serviço de Jupyter em todos os cabeçados.

![Reinicie o Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Reinicie o Jupyter")

## <a name="monitor-resources"></a>Monitorizar recursos

Lance a II de Fio como mostrado no início do artigo. Na tabela Métricas de Cluster em cima do ecrã, verifique os valores das colunas Memory **Used** e **Memory Total.** Se os dois valores estiverem próximos, pode não haver recursos suficientes para iniciar a próxima aplicação. O mesmo se aplica às colunas **VCores Utilizadas** e **VCores Total.** Além disso, na opinião geral, se houver uma candidatura no estado **aceite** e não transitar para **o ESTADO DE EXECUÇÃO** ou **FAILED,** isso também pode ser uma indicação de que não está a receber recursos suficientes para começar.

![Limite de Recursos](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite de Recursos")

## <a name="kill-running-applications"></a>Matar aplicações de execução

1. Na UI de fios, a partir do painel esquerdo, selecione **Running**. A partir da lista de aplicações em execução, determine o pedido a ser morto e selecione o **ID**.

    ![Mate app1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Mate app1")

2. Selecione **Kill Application** no canto superior direito e, em seguida, selecione **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>Consulte também

* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria insight de aplicação usando Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores apache spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
