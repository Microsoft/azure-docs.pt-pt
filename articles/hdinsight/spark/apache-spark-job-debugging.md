---
title: Depurar Apache Spark trabalhos em execução no Azure HDInsight
description: Use a interface do usuário do YARN, a interface do usuário do Spark e o servidor de histórico do Spark para rastrear e depurar trabalhos em execução em um cluster Spark no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 0e80aa44652efbc58f8259944058aabe59ca5d1a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338471"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar Apache Spark trabalhos em execução no Azure HDInsight

Neste artigo, você aprenderá a controlar e depurar [Apache Spark](https://spark.apache.org/) trabalhos em execução em clusters HDInsight usando a interface do usuário do [yarn, Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) a interface do usuário do Spark e o servidor de histórico do Spark. Você inicia um trabalho do Spark usando um notebook disponível com o cluster Spark **, Machine Learning: Análise preditiva em dados de inspeção de alimentos usando**MLLib. Você pode usar as etapas a seguir para acompanhar um aplicativo que você enviou usando qualquer outra abordagem também, por exemplo, **Spark-Submit**.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Você deve ter iniciado a execução do notebook **, [Machine Learning: Análise preditiva em dados de inspeção de alimentos usando](apache-spark-machine-learning-mllib-ipython.md)MLLib**. Para obter instruções sobre como executar este bloco de anotações, siga o link.  

## <a name="track-an-application-in-the-yarn-ui"></a>Rastrear um aplicativo na interface do usuário do amYARN

1. Inicie a interface do usuário do amYARN. Clique em **yarn** em **painéis de cluster**.

    ![portal do Azure iniciar a interface do usuário do YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Como alternativa, você também pode iniciar a interface do usuário do amYARN da interface do usuário do amAmbari. Para iniciar a interface do usuário do amAmbari, clique em **página inicial do Ambari** em **painéis do cluster**. Na interface do usuário do amAmbari, clique em **yarn**, clique em **links rápidos**, clique no Gerenciador de recursos ativo e clique em **interface do usuário do Gerenciador de recursos**.

2. Como você iniciou o trabalho do Spark usando notebooks Jupyter, o aplicativo tem o nome **remotesparkmagics** (esse é o nome de todos os aplicativos que são iniciados a partir dos notebooks). Clique na ID do aplicativo em relação ao nome do aplicativo para obter mais informações sobre o trabalho. Isso inicia a exibição do aplicativo.

    ![Servidor de histórico do Spark localizar ID do aplicativo Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    Para esses aplicativos que são iniciados a partir dos Notebooks Jupyter, o status é sempre **em execução** até que você saia do notebook.
3. Na exibição do aplicativo, você pode fazer uma busca detalhada para descobrir os contêineres associados ao aplicativo e os logs (stdout/stderr). Você também pode iniciar a interface do usuário do Spark clicando na vinculação correspondente à **URL de rastreamento**, conforme mostrado abaixo.

    ![Servidor de histórico do Spark-baixar logs de contêiner](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Acompanhar um aplicativo na interface do usuário do Spark

Na interface do usuário do Spark, você pode fazer uma busca detalhada nos trabalhos do Spark gerados pelo aplicativo iniciado anteriormente.

1. Para iniciar a interface do usuário do Spark, na exibição do aplicativo, clique no link em relação à **URL de rastreamento**, conforme mostrado na captura de tela acima. Você pode ver todos os trabalhos do Spark que são iniciados pelo aplicativo em execução no notebook Jupyter.

    ![Guia trabalhos do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Clique na guia **executores** para ver as informações de processamento e armazenamento de cada executor. Você também pode recuperar a pilha de chamadas clicando no link **despejo de thread** .

    ![Guia executores do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Clique na guia **estágios** para ver os estágios associados ao aplicativo.

    ![Guia estágios do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Exibir estágios do Spark")

    Cada estágio pode ter várias tarefas para as quais você pode exibir estatísticas de execução, como mostrado abaixo.

    ![Detalhes da guia estágios do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Exibir detalhes dos estágios do Spark")

4. Na página detalhes do estágio, você pode iniciar a visualização de DAG. Expanda o link de **Visualização DAG** na parte superior da página, conforme mostrado abaixo.

    ![Exibir estágios do Spark DAG visualização](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG ou Direct Aclyic Graph representa os diferentes estágios no aplicativo. Cada caixa azul no grafo representa uma operação Spark invocada do aplicativo.

5. Na página detalhes do estágio, você também pode iniciar o modo de exibição de linha do tempo do aplicativo. Expanda o link **linha do tempo do evento** na parte superior da página, conforme mostrado abaixo.

    ![Exibir linha do tempo de evento de estágios do Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Isso exibe os eventos do Spark na forma de uma linha do tempo. O modo de exibição de linha do tempo está disponível em três níveis, entre trabalhos, dentro de um trabalho e em um estágio. A imagem acima captura a exibição da linha do tempo para um determinado estágio.

   > [!TIP]  
   > Se você marcar a caixa de seleção **habilitar zoom** , poderá rolar para a esquerda e para a direita na exibição da linha do tempo.

6. Outras guias na interface do usuário do Spark também fornecem informações úteis sobre a instância do Spark.

   * Guia armazenamento – se seu aplicativo criar um RDDs, você poderá encontrar informações sobre eles na guia armazenamento.
   * Guia ambiente – essa guia fornece muitas informações úteis sobre sua instância do Spark, como:
     * Versão escalares
     * Diretório de log de eventos associado ao cluster
     * Número de núcleos de executor para o aplicativo
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Localizar informações sobre trabalhos concluídos usando o servidor de histórico do Spark

Quando um trabalho é concluído, as informações sobre o trabalho são mantidas no servidor de histórico do Spark.

1. Para iniciar o servidor de histórico do Spark, na folha visão geral, clique em **servidor de histórico do Spark** em painéis do **cluster**.

    ![Portal do Azure Iniciar servidor de histórico do Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "iniciar o histórico do Spark Server1")

   > [!TIP]  
   > Como alternativa, você também pode iniciar a interface do usuário do servidor de histórico do Spark da interface do usuário do amAmbari. Para iniciar a interface do usuário do amAmbari, na folha visão geral, clique em **Ambari página inicial** em **painéis do cluster**. Na interface do usuário do amAmbari, clique em **Spark**, clique em **links rápidos**e, em seguida, clique em **IU do servidor de histórico do Spark**.

2. Você verá todos os aplicativos concluídos listados. Clique em uma ID de aplicativo para fazer uma busca detalhada em um aplicativo para obter mais informações.

    ![Aplicativos concluídos do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-completed-applications.png "iniciar o histórico do Spark Server2")

## <a name="see-also"></a>Consulte também

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Depurar trabalhos de Apache Spark usando o servidor de histórico Spark estendido](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria do Application insights usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Para desenvolvedores do Spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
