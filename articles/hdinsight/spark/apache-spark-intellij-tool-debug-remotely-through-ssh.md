---
title: 'Azure Toolkit for IntelliJ: Depurar aplicativos Spark remotamente por meio de SSH '
description: Diretrizes passo a passo sobre como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos remotamente em clusters HDInsight por meio de SSH
keywords: depurar remotamente IntelliJ, depuração remota IntelliJ, SSH, IntelliJ, hdinsight, depurar IntelliJ, depuração
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 844901d9c689d5a04312b52101572854f4d7fe6b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994037"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar Apache Spark aplicativos em um cluster HDInsight com Azure Toolkit for IntelliJ por meio de SSH

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para depurar aplicativos remotamente em um cluster HDInsight. Para depurar seu projeto, você também pode exibir os [aplicativos Debug HDInsight Spark com Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) vídeo.

**Pré-requisitos**
* **Ferramentas do HDInsight no Azure Toolkit for IntelliJ**. Essa ferramenta faz parte do Azure Toolkit for IntelliJ. Para obter mais informações, consulte [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). E **Azure Toolkit for IntelliJ**. Use este kit de ferramentas para criar Apache Spark aplicativos para um cluster HDInsight. Para obter mais informações, siga as instruções em [usar Azure Toolkit for IntelliJ para criar Apache Spark aplicativos para um cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Serviço SSH do HDInsight com gerenciamento de nome de usuário e senha**. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [usar o túnel SSH para acessar a interface do usuário da Web do amAmbari, JobHistory, NameNode, Apache Oozie e outras UIs da Web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Saiba como executar a depuração e execução local
### <a name="scenario-1-create-a-spark-scala-application"></a>Cenário 1: Criar um aplicativo do Spark escalar 

1. Inicie o IntelliJ IDEA e, em seguida, crie um projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

   a. Selecione **Azure Spark/HDInsight**. 

   b. Selecione um modelo Java ou escalar com base em sua preferência. Selecione entre as seguintes opções:

   - **Projeto do Spark (Java)**

   - **Projeto do Spark (escala)**

   - **Projeto do Spark com amostras (escala)**

   - **Projeto do Spark com amostras de depuração de tarefa de falha (versão prévia) (escala)**

     Este exemplo usa um **projeto do Spark com modelo de amostras (escala)** .

   c. Na lista **Ferramenta de compilação**, selecione uma das seguintes opções, de acordo com as suas necessidades:

   - **Maven**, para o suporte de assistente de criação do projeto Scala

   - **SBT**, para gerir as dependências e a complicação do projeto Scala 

     ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **Seguinte**.     
 
1. Na próxima janela do **novo projeto** , faça o seguinte:

   ![Selecione o SDK do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista suspensa **SDK do projeto** , selecione **Java 1,8** para o cluster **Spark 2. x** ou selecione **Java 1,7** para o cluster **Spark 1. x** .

   c. Na lista suspensa **versão do Spark** , o assistente de criação de projeto escalar integra a versão correta para o SDK do Spark e o SDK do Scale. Se a versão do cluster Spark for anterior a 2,0, selecione **Spark 1. x**. Caso contrário, selecione **Spark 2. x.** Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)** .

   d. Selecione **Concluir**.

1. Selecione **src** >  **Main**escalarparaabrirseucódigonoprojeto. >  Este exemplo usa o script **SparkCore_wasbloTest** .

### <a name="prerequisite-for-windows"></a>Pré-requisito para o Windows
Enquanto estiver executando o aplicativo do Spark escala local em um computador Windows, você poderá receber uma exceção, conforme explicado no [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils. exe está ausente no Windows. 

Para resolver esse erro, [Baixe o executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione a variável de ambiente **HADOOP_HOME**e defina o valor da variável como **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Cenário 2: Executar execução local
1. Abra o script **SparkCore_wasbloTest** , clique com o botão direito do mouse no editor de scripts e selecione a opção **Executar ' [trabalho do Spark] xxx '** para executar a execução local.
1. Quando a execução local for concluída, você poderá ver o arquivo de saída salvo em seu **__padrão__** de **dados** > do explorador de projeto atual.

    ![Resultado da execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)
1. Nossas ferramentas definiram a configuração de execução local padrão automaticamente quando você executa a execução local e a depuração local. Abra a configuração **[Spark no hdinsight] xxx** no canto superior direito, você pode ver o **[Spark no hdinsight] xxx** já criado em **Apache Spark no HDInsight**. Alterne para a guia **executar localmente** .

    ![Configuração de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variáveis de ambiente](#prerequisite-for-windows): Se você já definiu a variável de ambiente do sistema **HADOOP_HOME** como **C:\WinUtils**, ela pode detectar automaticamente que não há necessidade de adicionar manualmente.
    - [Local do WinUtils. exe](#prerequisite-for-windows): Se você não tiver definido a variável de ambiente do sistema, poderá encontrar o local clicando em seu botão.
    - Basta escolher uma das duas opções e elas não são necessárias no MacOS e no Linux.
1. Você também pode definir a configuração manualmente antes de executar a depuração local e local. Na captura de tela anterior, selecione o sinal de **+** adição (). Em seguida, selecione a opção **Apache Spark no HDInsight** . Insira informações para **nome**, **nome da classe principal** para salvar e clique no botão Executar local.

### <a name="scenario-3-perform-local-debugging"></a>Cenário 3: Executar depuração local
1. Abra o script **SparkCore_wasbloTest** , defina pontos de interrupção.
1. Clique com o botão direito do mouse no editor de scripts e selecione a opção **depurar ' [Spark no HDInsight] xxx '** para executar a depuração local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Saiba como executar a execução e a depuração remotas
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: Executar execução remota

1. Para acessar o menu **Editar configurações** , selecione o ícone no canto superior direito. Nesse menu, você pode criar ou editar as configurações para depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Na caixa de diálogo **configurações de execução/depuração** , selecione o sinal de **+** adição (). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Alterne para **execução remota na guia cluster** . Insira informações para **nome**, **cluster do Spark**e **nome da classe principal**. Em seguida, clique em **Configuração avançada (depuração remota)** . Nossas ferramentas dão suporte àdepuração com executores. O **numExectors**, o valor padrão é 5. Você melhor não definiu mais que 3.

   ![Executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **Configuração avançada (depuração remota)** , selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário SSH e, em seguida, insira uma senha ou use um arquivo de chave privada. Se você quiser executar a depuração remota, precisará defini-la. Não é necessário defini-lo se você quiser usar a execução remota.

   ![Habilitar a depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A configuração agora é salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**. 

1. Depois de concluir as configurações, você pode executar o projeto no cluster remoto ou executar a depuração remota.
   
   ![Botão execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Clique no botão desconectar que os logs de envio não aparecem no painel esquerdo. No entanto, ele ainda está em execução no back-end.

   ![Resultado da execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: Executar depuração remota
1. Configure pontos de interrupção e clique no ícone de **depuração remota** . A diferença com o envio remoto é que o nome de usuário/senha do SSH precisa ser configurado.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Driver** e duas guias **executores** no painel **depurador** . Selecione o ícone **retomar programa** para continuar a execução do código, que atinge o próximo ponto de interrupção. Você precisa alternar para a guia **executor** correta para localizar o executor de destino a ser depurado. Você pode exibir os logs de execução na guia **console** correspondente.

   ![Guia depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: Executar depuração remota e correção de bugs
1. Configure dois pontos de interrupção e, em seguida, selecione o ícone de **depuração** para iniciar o processo de depuração remota.

1. O código para no primeiro ponto de interrupção, e as informações de parâmetro e variável são mostradas no painel **variáveis** . 

1. Selecione o ícone **retomar programa** para continuar. O código para no segundo ponto. A exceção é detectada como esperado.

   ![Erro de geração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Selecione o ícone **retomar programa** novamente. A janela de **envio do HDInsight Spark** exibe um erro "falha na execução do trabalho".

   ![Envio de erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Para atualizar dinamicamente o valor da variável usando o recurso de depuração IntelliJ, selecione **depurar** novamente. O painel **variáveis** é exibido novamente. 

1. Clique com o botão direito do mouse no destino na guia **depurar** e selecione **definir valor**. Em seguida, insira um novo valor para a variável. Em seguida, selecione **Enter** para salvar o valor. 

   ![Definir valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecione o ícone **retomar programa** para continuar a executar o programa. Desta vez, nenhuma exceção é detectada. Você pode ver que o projeto é executado com êxito sem exceções.

   ![Depurar sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Passos seguintes
* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar projeto escalar (vídeo): [Criar Apache Spark aplicativos escalares](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
