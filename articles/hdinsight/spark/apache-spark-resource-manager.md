---
title: Gerir recursos para o cluster Apache Spark em Azure HDInsight
description: Aprenda a gerir recursos para clusters Spark em Azure HDInsight para um melhor desempenho.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929809"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerir recursos para o cluster Apache Spark em Azure HDInsight

Aprenda a aceder às interfaces como [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI e o [Spark History Server](./apache-azure-spark-history-server.md) associado ao seu cluster [Apache Spark,](https://spark.apache.org/) e como sintonizar a configuração do cluster para um desempenho ideal.

## <a name="open-the-spark-history-server"></a>Abra o Servidor histórico de faíscas

Spark History Server é o UI web para aplicações Spark concluídas e executando. É uma extensão da Web UI da Spark. Para obter informações completas, consulte [o Spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Abra o Yarn UI

Pode utilizar o UI YARN para monitorizar as aplicações que estão atualmente em execução no cluster Spark.

1. A partir do [portal Azure,](https://portal.azure.com/)abra o aglomerado de faíscas. Para obter mais informações, consulte [List e mostrar clusters.](../hdinsight-administer-use-portal-linux.md#showClusters)

2. A partir **de dashboards cluster,** selecione **Yarn**. Quando solicitado, insira as credenciais de administração para o cluster Spark.

    ![Lançamento YARN UI](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Em alternativa, também pode lançar o UI YARN a partir da UI Ambari. A partir do Ambari UI, navegue para **YARN**  >  **Quick Links**  >  **Ative** Resource  >  **Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicações Spark

Os três parâmetros-chave que podem ser utilizados para a configuração da Faísca, dependendo dos requisitos de aplicação, são `spark.executor.instances` `spark.executor.cores` , e `spark.executor.memory` . Um Executor é um processo lançado para uma aplicação Spark. Funciona no nó do trabalhador e é responsável pela execução das tarefas da candidatura. O número predefinido dos executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalhador e no tamanho do nó do trabalhador. Esta informação é armazenada `spark-defaults.conf` nos nós da cabeça do cluster.

Os três parâmetros de configuração podem ser configurados ao nível do cluster (para todas as aplicações que funcionam no cluster) ou podem ser especificados para cada aplicação individual.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando Ambari UI

1. A partir da UI Ambari navegue até **Spark2**  >  **Configs**  >  **Custom spark2-defaults**.

    ![Definir parâmetros usando o costume de Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Definir parâmetros usando o costume de Ambari")

1. Os valores predefinidos são bons para ter quatro aplicações Spark executadas simultaneamente no cluster. Pode alterar estes valores a partir da interface do utilizador, como mostra a seguinte imagem:

    ![Definir parâmetros usando Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Definir parâmetros usando Ambari")

1. **Selecione Guardar** para guardar as alterações de configuração. No topo da página, é solicitado a reiniciar todos os serviços afetados. **Selecione Reiniciar**.

    ![Reiniciar serviços](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros para uma aplicação em execução no Caderno Jupyter

Para aplicações em execução no Bloco de Notas do Jupyter, pode utilizar a `%%configure` magia para fazer as alterações de configuração. Idealmente, deve fazer tais alterações no início da aplicação, antes de executar a sua primeira célula de código. Ao fazê-lo, a configuração é aplicada à sessão Livy, quando é criada. Se pretender alterar a configuração numa fase posterior da aplicação, deve utilizar o `-f` parâmetro. No entanto, ao fazê-lo, perdem-se todos os progressos no pedido.

O seguinte corte mostra como alterar a configuração para uma aplicação em execução em Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Os parâmetros de configuração devem ser passados como uma corda JSON e devem estar na linha seguinte após a magia, como mostra a coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros para um pedido submetido com a apresentação de faíscas

O comando seguinte é um exemplo de como alterar os parâmetros de configuração de uma aplicação de lote que é submetida através `spark-submit` de .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros para uma aplicação submetida com recurso ao cURL

O seguinte comando é um exemplo de como alterar os parâmetros de configuração de uma aplicação de lote que é submetida usando o cURL.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Copie o ficheiro JAR para a sua conta de armazenamento de cluster. Não copie o ficheiro JAR diretamente para o nó de cabeça.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Altere estes parâmetros num servidor Spark Thrift

O Spark Thrift Server fornece acesso JDBC/ODBC a um cluster Spark e é utilizado para atender consultas Spark SQL. Ferramentas como Power BI, Tableau, e assim por diante, use o protocolo ODBC para comunicar com o Spark Thrift Server para executar consultas Spark SQL como uma Aplicação spark. Quando um cluster Spark é criado, duas instâncias do Spark Thrift Server são iniciadas, uma em cada nó de cabeça. Cada Spark Thrift Server é visível como uma aplicação Spark na UI YARN.

O Spark Thrift Server utiliza a atribuição de executor dinâmico spark e, portanto, `spark.executor.instances` o não é utilizado. Em vez disso, o Spark Thrift Server utiliza `spark.dynamicAllocation.maxExecutors` e especifica a contagem do `spark.dynamicAllocation.minExecutors` executor. Os parâmetros de configuração `spark.executor.cores` , e `spark.executor.memory` são usados para modificar o tamanho do executor. Pode alterar estes parâmetros como mostrado nos seguintes passos:

* Expandir a categoria **Advanced spark2-thrift-sparkconf** para atualizar os parâmetros `spark.dynamicAllocation.maxExecutors` , e `spark.dynamicAllocation.minExecutors` .

    ![Configure o servidor Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configure o servidor Spark Thrift")

* Expandir a categoria **De sparkconf-spark-thrift-thrift personalizado** para atualizar os parâmetros `spark.executor.cores` e `spark.executor.memory` .

    ![Configure o parâmetro do servidor Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configure o parâmetro do servidor Spark Thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Alterar a memória do controlador do Servidor Spark Thrift

A memória do controlador do Spark Thrift Server está configurada para 25% do tamanho do nó de cabeça RAM, desde que o tamanho total da RAM do nó da cabeça seja superior a 14 GB. Pode utilizar o UI Ambari para alterar a configuração da memória do condutor, como mostra a seguinte imagem:

A partir da UI Ambari, navegue até **Spark2**  >  **Configs**  >  **Advanced spark2-env**. Em seguida, forneça o valor para **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos de cluster spark

Devido à alocação dinâmica da Spark, os únicos recursos que são consumidos pelo servidor thrift são os recursos para os dois mestres de aplicações. Para recuperar estes recursos, tem de parar os serviços do Servidor Thrift em funcionamento no cluster.

1. A partir da UI Ambari, a partir do painel esquerdo, selecione **Spark2**.

2. Na página seguinte, selecione **Spark2 Thrift Servers**.

    ![Reiniciar o servidor thrift1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Reiniciar o servidor thrift1")

3. Deve ver os dois headnodes em que o Spark2 Thrift Server está a funcionar. Selecione um dos headnodes.

    ![Reiniciar o servidor thrift2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Reiniciar o servidor thrift2")

4. A página seguinte lista todos os serviços em execução nesse headnode. Na lista, selecione o botão drop-down ao lado do Spark2 Thrift Server e, em seguida, selecione **Stop**.

    ![Reiniciar o servidor thrift3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Reiniciar o servidor thrift3")
5. Repita estes passos no outro cabeça também.

## <a name="restart-the-jupyter-service"></a>Reiniciar o serviço Jupyter

Lance o Ambari Web UI como mostrado no início do artigo. A partir do painel de navegação à esquerda, selecione **Jupyter**, selecione **Service Actions**, e, em seguida, selecione **Restart All**. Isto começa o serviço Jupyter em todos os headnodes.

![Reinicie o Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Reinicie o Jupyter")

## <a name="monitor-resources"></a>Monitorizar recursos

Lance a UI yarn como mostrado no início do artigo. Na tabela Métricas de Cluster no topo do ecrã, verifique os valores das colunas **Memory Used** e **Memory Total.** Se os dois valores estiverem próximos, pode não haver recursos suficientes para iniciar a próxima aplicação. O mesmo se aplica às **colunas VCores Used** e **VCores Total.** Além disso, na opinião principal, se houver uma aplicação mantida no estado **aceite** e não transitar para **o** estado RUNNING ou **FAILED,** isso também pode ser uma indicação de que não está a receber recursos suficientes para começar.

![Limite de recursos](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite de recursos")

## <a name="kill-running-applications"></a>Matar aplicações de execução

1. Na UI yarn, a partir do painel esquerdo, selecione **Running**. A partir da lista de aplicações em execução, determine o pedido a ser morto e selecione o **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Selecione **Kill Application** no canto superior direito e, em seguida, selecione **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>Veja também

* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria de insight de aplicação usando Apache Spark em HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores de Faíscas Apache

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight Plugin para IntelliJ IDEA para depurar as aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
