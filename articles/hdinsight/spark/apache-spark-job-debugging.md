---
title: Depurar tarefas do Apache Spark em execução no Azure HDInsight
description: Utilize a IU do YARN, interface do Usuário do Spark e o servidor de histórico do Spark para controlar e depurar tarefas em execução num cluster do Spark no Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: 5e384520c1b8d6cf5e3b182bbddf41a5f4f7f8f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124293"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar tarefas do Apache Spark em execução no Azure HDInsight

Neste artigo, irá aprender a controlar e depurar [Apache Spark](https://spark.apache.org/) tarefas em execução HDInsight clusters com o [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) interface do Usuário, interface do Usuário do Spark e o servidor de histórico do Spark. Iniciar uma tarefa do Spark com um bloco de notas disponível com o cluster do Spark, **Machine learning: Análise Preditiva em dados de inspeção de comida usando MLLib**. Pode utilizar os seguintes passos para controlar um aplicativo que enviados através de qualquer outra abordagem, por exemplo, **spark-submit**.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Deve iniciar a executar o bloco de notas  **[Machine learning: Análise Preditiva em dados de inspeção de comida usando MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este bloco de notas, siga a ligação.  

## <a name="track-an-application-in-the-yarn-ui"></a>Monitorizar uma aplicação na IU do YARN
1. Inicie a IU do YARN. Clique em **Yarn** sob **dashboards de clusters**.
   
    ![Iniciar a IU do YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]  
   > Em alternativa, também pode iniciar a IU do YARN do IU do Ambari. Para iniciar a IU do Ambari, clique em **Ambari doméstica** sob **dashboards de clusters**. A partir da IU do Ambari, clique em **YARN**, clique em **ligações rápidas**, clique em Gestor de recursos do Active Directory e, em seguida, clique em **da IU do Gestor de recursos**. 

2. Uma vez que iniciou a tarefa do Spark com blocos de notas do Jupyter, o aplicativo tem o nome **remotesparkmagics** (este é o nome para todas as aplicações que são iniciadas a partir de blocos de notas). Clique no ID da aplicação contra o nome da aplicação para obter mais informações sobre a tarefa. Esta ação inicia o modo de exibição do aplicativo.
   
    ![Localizar o ID de aplicação do Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Para essas aplicações que são iniciadas a partir de blocos de notas do Jupyter, o estado é sempre **em execução** até sair o bloco de notas.
3. Na vista de aplicação, pode desagregar com Saiba de que os contentores associados com o aplicativo e os registos (stdout/stderr). Também pode iniciar a interface do Usuário do Spark ao clicar em ligação correspondente para o **URL de controlo**, conforme mostrado abaixo. 
   
    ![Transferir registos de contentor](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Monitorizar uma aplicação na IU do Spark
Na IU do Spark, pode desagregar para as tarefas do Spark que são geradas pela aplicação iniciada anteriormente.

1. Para iniciar a IU do Spark, da vista do aplicativo, clique na ligação em relação a **URL de controlo**, conforme mostrado na captura de ecrã acima. Pode ver todos os trabalhos do Spark que são iniciados pela aplicação em execução no bloco de notas do Jupyter.
   
    ![Ver tarefas do Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Clique nas **executores** separador para ver informações de processamento e armazenamento para cada executor. Também pode obter a pilha de chamadas ao clicar no **Thread Despejar** ligação.
   
    ![Ver o executor do Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Clique nas **fases** guia para ver as fases associadas à aplicação.
   
    ![Ver as fases de Spark](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Cada fase pode ter várias tarefas para o qual pode ver estatísticas de execução, como mostrado abaixo.
   
    ![Ver as fases de Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Na página de detalhes estágio, pode iniciar visualização DAG. Expanda a **DAG visualização** link na parte superior da página, conforme mostrado abaixo.
   
    ![Ver a visualização de fases DAG do Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG ou direto Aclyic gráfico representa as fases diferentes do aplicativo. Cada caixa azul no gráfico representa uma operação de Spark invocada a partir da aplicação.
5. Na página de detalhes estágio, também pode iniciar a vista de linha cronológica do aplicativo. Expanda a **linha cronológica de evento** link na parte superior da página, conforme mostrado abaixo.
   
    ![Ver linha cronológica de eventos do Spark fases](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Esta ação apresenta os eventos do Spark na forma de uma linha do tempo. A vista de linha cronológica está disponível em três níveis, em tarefas, dentro de uma tarefa e dentro de uma fase. A imagem acima captura a vista de linha cronológica para uma determinada fase.
   
   > [!TIP]  
   > Se selecionar a **ativar o zoom** caixa de verificação, pode rolar à esquerda e à direita em toda a vista de linha do tempo.

6. Outros separadores na IU do Spark fornecem informações úteis sobre a instância do Spark.
   
   * Separador de armazenamento - se a sua aplicação cria um RDDs, pode encontrar informações sobre eles no separador de armazenamento.
   * Separador de ambiente - este separador fornece muitas informações úteis sobre a sua instância do Spark, tais como o 
     * Versão de scala
     * Diretório de registo de eventos associado ao cluster
     * Número de núcleos de executor da aplicação
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Encontre informações sobre as tarefas concluídas com o servidor de histórico do Spark
Assim que uma tarefa é concluída, as informações sobre a tarefa são mantidas no servidor de histórico de Spark.

1. Para iniciar o servidor de histórico do Spark, no painel Descrição geral, clique em **servidor de histórico do Spark** sob **dashboards de clusters**.
   
    ![Inicie o servidor de histórico do Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]  
   > Em alternativa, pode também iniciar a interface do Usuário do Spark histórico de servidor da IU do Ambari. Para iniciar a IU do Ambari, no painel Descrição geral, clique em **Ambari doméstica** sob **dashboards de clusters**. A partir da IU do Ambari, clique em **Spark**, clique em **ligações rápidas**e, em seguida, clique em **da IU do servidor de histórico de Spark**.

2. Ver todos os aplicativos concluídos listados. Clique num ID de aplicação para fazer uma busca detalhada num aplicativo para obter mais informações.
   
    ![Inicie o servidor de histórico do Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Consulte também
*  [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
*  [Depurar tarefas do Apache Spark com o servidor de histórico expandido do Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise dados Application Insight telemetria com o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Para desenvolvedores do Spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
