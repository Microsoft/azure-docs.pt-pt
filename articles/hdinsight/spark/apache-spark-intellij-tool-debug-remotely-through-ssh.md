---
title: 'Azure Toolkit for IntelliJ: depurar aplicativos Spark com SSH-HDInsight'
description: Diretrizes passo a passo sobre como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos remotamente em clusters HDInsight por meio de SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 36e665f889ece48661a859e1a9a44f23aec08c37
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548887"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar Apache Spark aplicativos em um cluster HDInsight com Azure Toolkit for IntelliJ por meio de SSH

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para depurar aplicativos remotamente em um cluster HDInsight. Para depurar seu projeto, você também pode exibir os [aplicativos Debug HDInsight Spark com Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) vídeo.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Consulte [criar um cluster apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Para usuários do Windows: enquanto você está executando o aplicativo local do Spark escalar em um computador Windows, você pode obter uma exceção, conforme explicado no [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils. exe está ausente no Windows.

    Para resolver esse erro, [Baixe o executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione a variável de ambiente **HADOOP_HOME**e defina o valor da variável como **C:\WinUtils**.

* [INTELLIJ Idea](https://www.jetbrains.com/idea/download/#section=windows) (a Community Edition é gratuita).

* [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-installation).

* [Plug-in escalar para IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Criar um aplicativo do Spark escalar

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir a janela **novo projeto** .

1. Selecione **Apache Spark/HDInsight** no painel esquerdo.

1. Selecione **projeto do Spark com amostras (escala)** na janela principal.

1. Na lista suspensa **ferramenta de compilação** , selecione uma das seguintes opções:

    * Suporte ao assistente de criação de projeto do **Maven** para escalabilidade.
    * **SBT** para gerenciar as dependências e compilar para o projeto escalar.

     ![IntelliJ criar novo projeto Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecione **Seguinte**.

1. Na próxima janela **novo projeto** , forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome do projeto|Insira um nome. Essa orientação usa `myApp`.|
    |Local do projeto|Insira o local desejado para salvar o projeto.|
    |SDK do projeto|Se estiver em branco, selecione **novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada do SDK do Spark e do SDK do Scale. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2. x.** . Este exemplo usa o **Spark 2.3.0 (escala 2.11.8)** .|

   ![IntelliJ novo projeto selecione versão do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecione **Concluir**. Pode levar alguns minutos para que o projeto fique disponível. Observe o canto inferior direito do progresso.

1. Expanda seu projeto e navegue até **src** > **principal** > **escala** de > de **amostra**. Clique duas vezes em **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Executar execução local

1. No script de **SparkCore_WasbIOTest** , clique com o botão direito do mouse no editor de scripts e selecione a opção **Executar ' SparkCore_WasbIOTest '** para executar a execução local.

1. Quando a execução local for concluída, você poderá ver o arquivo de saída salvo em seus **dados** atuais do explorador de projeto >  **__padrão__** .

    ![Resultado da execução local do projeto IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Nossas ferramentas definiram a configuração de execução local padrão automaticamente quando você executa a execução local e a depuração local. Abra a configuração **[Spark no hdinsight] xxx** no canto superior direito, você pode ver o **[Spark no hdinsight] xxx** já criado em **Apache Spark no HDInsight**. Alterne para a guia **executar localmente** .

    ![Executar IntelliJ executar configurações de depuração local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Variáveis de ambiente](#prerequisites): se você já definiu a variável de ambiente do sistema **HADOOP_HOME** como **C:\WinUtils**, ela pode detectar automaticamente que não há necessidade de adicionar manualmente.
    - [Local do WinUtils. exe](#prerequisites): se você não tiver definido a variável de ambiente do sistema, poderá encontrar o local clicando em seu botão.
    - Basta escolher uma das duas opções e elas não são necessárias no MacOS e no Linux.

1. Você também pode definir a configuração manualmente antes de executar a depuração local e local. Na captura de tela anterior, selecione o sinal de adição ( **+** ). Em seguida, selecione a opção **Apache Spark no HDInsight** . Insira informações para **nome**, **nome da classe principal** para salvar e clique no botão Executar local.

## <a name="perform-local-debugging"></a>Executar depuração local

1. Abra o script **SparkCore_wasbloTest** , defina pontos de interrupção.

1. Clique com o botão direito do mouse no editor de scripts e selecione a opção **depurar ' [Spark no HDInsight] xxx '** para executar a depuração local.

## <a name="perform-remote-run"></a>Executar execução remota

1. Navegue até **executar** > **Editar configurações...** . Nesse menu, você pode criar ou editar as configurações para depuração remota.

1. Na caixa de diálogo **configurações de execução/depuração** , selecione o sinal de adição ( **+** ). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![IntelliJ Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Alterne para **execução remota na guia cluster** . Insira informações para **nome**, **cluster Spark**e **nome de classe principal**. Em seguida, clique em **Configuração avançada (depuração remota)** . Nossas ferramentas dão suporte à depuração com **executores**. O **numExectors**, o valor padrão é 5. Você melhor não definiu mais que 3.

   ![IntelliJ executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **Configuração avançada (depuração remota)** , selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário SSH e, em seguida, insira uma senha ou use um arquivo de chave privada. Se você quiser executar a depuração remota, precisará defini-la. Não é necessário defini-lo se você quiser usar a execução remota.

   ![Configuração avançada do IntelliJ habilitar a depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A configuração agora é salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**.

1. Depois de concluir as configurações, você pode executar o projeto no cluster remoto ou executar a depuração remota.

   ![Botão de execução remota do trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Clique no botão **Desconectar** que os logs de envio não aparecem no painel esquerdo. No entanto, ele ainda está em execução no back-end.

   ![Resultado da execução remota do trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Executar depuração remota

1. Configure pontos de interrupção e clique no ícone de **depuração remota** . A diferença com o envio remoto é que o nome de usuário/senha do SSH precisa ser configurado.

   ![Ícone de depuração do trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Driver** e duas guias **executores** no painel **depurador** . Selecione o ícone **retomar programa** para continuar a execução do código, que atinge o próximo ponto de interrupção. Você precisa alternar para a guia **executor** correta para localizar o executor de destino a ser depurado. Você pode exibir os logs de execução na guia **console** correspondente.

   ![Guia de depuração de trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Executar depuração remota e correção de bugs

1. Configure dois pontos de interrupção e, em seguida, selecione o ícone de **depuração** para iniciar o processo de depuração remota.

1. O código para no primeiro ponto de interrupção, e as informações de parâmetro e variável são mostradas no painel **variáveis** .

1. Selecione o ícone **retomar programa** para continuar. O código para no segundo ponto. A exceção é detectada como esperado.

   ![Erro de geração de trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecione o ícone **retomar programa** novamente. A janela de **envio do HDInsight Spark** exibe um erro "falha na execução do trabalho".

   ![Envio de erro de trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Para atualizar dinamicamente o valor da variável usando o recurso de depuração IntelliJ, selecione **depurar** novamente. O painel **variáveis** é exibido novamente.

1. Clique com o botão direito do mouse no destino na guia **depurar** e selecione **definir valor**. Em seguida, insira um novo valor para a variável. Em seguida, selecione **Enter** para salvar o valor.

   ![Valor do conjunto de trabalhos do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecione o ícone **retomar programa** para continuar a executar o programa. Desta vez, nenhuma exceção é detectada. Você pode ver que o projeto é executado com êxito sem exceções.

   ![Trabalho do Spark remoto do IntelliJ de depuração sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto escalar (vídeo): [criar Apache Spark aplicativos escalares](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Use Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de compilação usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
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
