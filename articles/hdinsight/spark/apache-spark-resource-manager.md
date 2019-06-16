---
title: Gerir os recursos de cluster do Apache Spark no Azure HDInsight
description: Saiba como utilizar Gerir os recursos de clusters do Spark no HDInsight do Azure para um melhor desempenho.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: dfd926f3e091f4516685fd73f3a6b87949330afb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706361"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerir os recursos de cluster do Apache Spark no Azure HDInsight 

Saiba como aceder as interfaces, como ao [Apache Ambari](https://ambari.apache.org/) interface do Usuário, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) interface do Usuário e o servidor de histórico do Spark associado sua [Apache Spark](https://spark.apache.org/) cluster e como Otimize a configuração de cluster para um desempenho ideal.

**Pré-requisitos:**

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Abra a IU Web do Ambari

Apache Ambari é utilizado para monitorizar o cluster e fazer alterações de configuração. Para obter mais informações, consulte [Apache Hadoop gerir clusters no HDInsight com o portal do Azure](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Abra o servidor de histórico do Spark

Servidor de histórico do Spark é a IU da web para aplicações do Spark em execução e concluídas. É uma extensão da IU da Web do Spark.

**Para abrir a interface do Usuário da Web de servidor de histórico de Spark**

1. Partir do [portal do Azure](https://portal.azure.com/), abra o cluster do Spark. Para obter mais informações, consulte [listar e Mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Partir **ligações rápidas**, clique em **Dashboard do Cluster**e, em seguida, clique em **servidor de histórico do Spark**

    ![Servidor de histórico do spark](./media/apache-spark-resource-manager/launch-history-server.png "Server do histórico do Spark")

    Quando lhe for pedido, introduza as credenciais de administrador para o cluster do Spark. Também pode abrir o servidor de histórico do Spark ao navegar para o seguinte URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Substitua `<ClusterName>` com o nome de cluster do Spark.

A web do servidor de histórico do Spark a interface do Usuário é semelhante a:

![Servidor de histórico do Spark do HDInsight](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Abra o IU do Yarn
Pode utilizar a IU do YARN para monitorizar aplicações atualmente em execução no cluster do Spark.

1. Partir do [portal do Azure](https://portal.azure.com/), abra o cluster do Spark. Para obter mais informações, consulte [listar e Mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Partir **ligações rápidas**, clique em **Dashboard do Cluster**e, em seguida, clique em **YARN**.

    ![Iniciar a IU do YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Em alternativa, também pode iniciar a IU do YARN do IU do Ambari. Para iniciar a IU do Ambari, clique em **Dashboard de clusters**e, em seguida, clique em **Dashboard de clusters do HDInsight**. A partir da IU do Ambari, clique em **YARN**, clique em **ligações rápidas**, clique em Gestor de recursos do Active Directory e, em seguida, clique em **da IU do Gestor de recursos**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicações do Spark
Os três parâmetros de chave que podem ser utilizados para a configuração de Spark dependendo dos requisitos de aplicação são `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado para uma aplicação de Spark. Ele é executado no nó de trabalho e é responsável para executar as tarefas para a aplicação. O número predefinido de executores e os tamanhos de executor para cada cluster é calculado com base no número de nós de trabalho e o tamanho de nó de trabalho. Essas informações são armazenadas no `spark-defaults.conf` sobre os nós principais do cluster.

Os parâmetros de configuração de três podem ser configurados no nível do cluster (para todas as aplicações executadas no cluster) ou podem ser especificados para cada aplicativo individual também.

### <a name="change-the-parameters-using-ambari-ui"></a>Altere os parâmetros usando a IU do Ambari
1. De que o clique de IU do Ambari **Spark**, clique em **configurações**e, em seguida, expanda **Custom spark-predefinições**.

    ![Definir parâmetros com o Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Os valores predefinidos são bons ter quatro de Spark de aplicativos executado em simultâneo no cluster. Pode alterar estes valores de interface do usuário, conforme mostrado na captura de ecrã seguinte:

    ![Definir parâmetros com o Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Clique em **guardar** para guardar as alterações de configuração. Na parte superior da página, lhe for pedido para reiniciar todos os serviços afetados. Clique em **Reiniciar**.

    ![Reinicie os serviços](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Altere os parâmetros para uma aplicação em execução no bloco de notas do Jupyter
Para aplicações em execução no bloco de notas Jupyter, pode usar o `%%configure` mágica para fazer as alterações de configuração. Idealmente, deve efetuar essas alterações no início do aplicativo, antes de executar a sua primeira célula do código. Fazer isto garante que a configuração é aplicada à sessão do Livy, quando é criada. Se pretender alterar a configuração num estágio posterior na aplicação, tem de utilizar o `-f` parâmetro. No entanto, ao fazer isso, todo o progresso no aplicativo é perdido.

O fragmento seguinte mostra como alterar a configuração para uma aplicação em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parâmetros de configuração devem ser passados como uma cadeia de caracteres do JSON e tem de ser na próxima linha posterior a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Altere os parâmetros para uma aplicação submetida com o spark-submit
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para uma aplicação de lote submetido com `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Altere os parâmetros para uma aplicação submetida com cURL
O comando seguinte é um exemplo de como alterar os parâmetros de configuração para uma aplicação de lote submetido com o cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Alterar estes parâmetros num servidor Spark Thrift
Spark Thrift Server fornece acesso JDBC/ODBC para um cluster do Spark e é utilizado para consultas de Spark SQL de serviço. Ferramentas como o Power BI, Tableau etc. Utilize o protocolo ODBC para comunicar com o Spark Thrift Server para executar consultas do Spark SQL como um aplicativo de Spark. Quando é criado um cluster do Spark, estão a usar duas instâncias do servidor Spark Thrift, um em cada nó principal. Cada servidor do Spark Thrift é visível como um aplicativo de Spark na IU do YARN.

Spark Thrift Server utiliza a alocação de dinâmico do executor do Spark e, por conseguinte, o `spark.executor.instances` não é utilizado. Em vez disso, utiliza o servidor do Spark Thrift `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` para especificar a contagem de executor. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` é usada para modificar o tamanho de executor. Pode alterar esses parâmetros, conforme mostrado nas etapas a seguir:

* Expanda a **Advanced spark-thrift-sparkconf** categoria para atualizar os parâmetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, e `spark.executor.memory`.

    ![Configurar o Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expanda a **personalizado spark-thrift-sparkconf** categoria para atualizar o parâmetro `spark.executor.cores`.

    ![Configurar o Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Altere a memória de controlador do servidor Spark Thrift
Memória de controlador do Spark Thrift Server está configurado para 25% do tamanho de RAM do nó principal, contanto que o tamanho de RAM total do nó principal é superior a 14 GB. Pode utilizar a IU do Ambari para alterar a configuração de memória de driver, conforme mostrado na captura de ecrã seguinte:

* De que o clique de IU do Ambari **Spark**, clique em **configurações**, expanda **Advanced spark env**e, em seguida, forneça o valor para **spark_thrift_cmd_opts**.

    ![Configurar o servidor do Spark thrift RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos de cluster do Spark
Devido a alocação de dinâmica do Spark, os únicos recursos que são consumidos pelo servidor de thrift são os recursos para os mestres de duas aplicações. Para recuperar esses recursos, tem de parar os serviços de Thrift Server em execução no cluster.

1. A partir da IU do Ambari, no painel à esquerda, clique em **Spark**.
2. Na página seguinte, clique em **Spark Thrift servidores**.

    ![Reinicie o servidor de thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Deverá ver dois nós principais no qual o servidor Spark Thrift está em execução. Clique num de nós principais.

    ![Reinicie o servidor de thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. A página seguinte apresenta uma lista de todos os serviços em execução nesse nó principal. Na lista, clique no botão de menu pendente junto ao servidor do Spark Thrift e, em seguida, clique em **parar**.

    ![Reinicie o servidor de thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Repita estes passos no outro nó principal também.

## <a name="restart-the-jupyter-service"></a>Reinicie o serviço do Jupyter
Inicie a IU da Web de Ambari, conforme mostrado no início do artigo. No painel de navegação esquerdo, clique em **Jupyter**, clique em **ações de serviço**e, em seguida, clique em **reiniciar todos os**. Esta ação inicia o serviço Jupyter em todos os principais.

![Reinicie o Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "reiniciar Jupyter")

## <a name="monitor-resources"></a>Monitorizar recursos
Inicie a IU do Yarn, conforme mostrado no início do artigo. Na tabela de métricas de Cluster na parte superior do ecrã, verifique os valores da **memória utilizada** e **memória Total** colunas. Se os dois valores forem fechar, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica para o **VCores utilizados** e **Total de VCores** colunas. Além disso, na exibição principal, se existir uma aplicação básica no **aceite** estado e não entrar **em execução** nem **falha** de estado, também pode ser uma indicação de que ele não estiver a receber recursos suficientes para iniciar.

![Limite de recursos](./media/apache-spark-resource-manager/resource-limit.png "limite de recursos")

## <a name="kill-running-applications"></a>Eliminar aplicações em execução
1. Na IU do Yarn, do painel esquerdo, clique em **em execução**. Na lista de aplicativos em execução, determinar o aplicativo a ser terminadas e clique nas **ID**.

    ![Eliminar App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Clique em **Kill aplicativo** no canto superior direito, em seguida, clique em **OK**.

    ![Eliminar App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Consulte também
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise dados Application Insight telemetria com o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores do Apache Spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
