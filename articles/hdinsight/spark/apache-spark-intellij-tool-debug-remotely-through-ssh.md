---
title: 'Kit de ferramentas Azure para IntelliJ: Debug Spark apps com SSH - HDInsight'
description: Orientação passo a passo sobre como usar ferramentas HDInsight no Kit de Ferramentas Azure para intelliJ para depurar aplicações remotamente em clusters HDInsight através do SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: c032e900cd2f58581517b08905d5b0660ed8bbda
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857808"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Aplicações Debug Apache Spark num cluster HDInsight com Kit de Ferramentas Azure para IntelliJ através de SSH

Este artigo fornece orientações passo a passo sobre como usar ferramentas HDInsight no [Kit de Ferramentas Azure para intelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij) para depurar aplicações remotamente num cluster HDInsight. Para desinserir o seu projeto, também pode ver as [aplicações Debug HDInsight Spark com o Azure Toolkit para vídeo IntelliJ.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Para os utilizadores do Windows: Enquanto estiver a executar a aplicação Spark Scala local num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque winUtils.exe está desaparecido no Windows.

    Para resolver este erro, baixe [Winutils.exe](https://github.com/steveloughran/winutils) para um local como **C:\WinUtils\bin**. Em seguida, adicione a variável ambiental **HADOOP_HOME,** e detetete o valor da variável para **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (A edição comunitária é gratuita.).

* [Kit de ferramentas Azure para IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Plugin Scala para IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Criar uma aplicação Spark Scala

1. Inicie o IntelliJ IDEA e selecione **Create New Project** para abrir a janela new **project.**

1. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.

1. Selecione **Projeto Spark com Amostras (Scala)** a partir da janela principal.

1. A partir da lista de drop-down da **ferramenta Build,** selecione uma das seguintes:

    * **Maven** para o apoio ao assistente de criação de projetos scala.
    * **SBT** para gerir as dependências e construção para o projeto Scala.

     ![Intellij Criar Novo Projeto Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecione **Seguinte**.

1. Na próxima janela do **Novo Projeto,** forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome do projeto|Insira um nome. Esta caminhada `myApp`através de usos.|
    |Localização do projeto|Insira o local desejado para salvar o seu projeto.|
    |Projeto SDK|Se estiver em branco, selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x.**. Este exemplo utiliza **o Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij New Project seleciona versão Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecione **Concluir**. Pode levar alguns minutos até que o projeto fique disponível. Observe o canto inferior direito para progredir.

1. Expanda o seu projeto e navegue até à**amostra****scala** > **principal** > do **SRC** > . Clique duplo **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Realizar corridalocal

1. A partir do guião **SparkCore_WasbIOTest,** clique no editor de script seletiva e, em seguida, selecione a opção **Executar 'SparkCore_WasbIOTest'** para executar a execução local.

1. Uma vez concluída a execução local, pode ver o ficheiro de saída guardar para o padrão de **dados** > **__do__** explorador do projeto atual .

    ![Resultado da execução local do Projeto Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. As nossas ferramentas definiram automaticamente a configuração de execução local padrão quando executa a execução local e o depurado local. Abra a configuração **[Spark on HDInsight] XXX** no canto superior direito, pode ver a **[Faísca no HDInsight]XXX** já criada sob **Apache Spark no HDInsight**. Mude para separador **Local Run.**

    ![Intellij Executar configurações locais de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Variáveis ambientais](#prerequisites): Se já definir a variável ambiente do sistema **HADOOP_HOME** a **C:\WinUtils,** pode detetar automaticamente que não é necessário adicionar manualmente.
    - [WinUtils.exe Localização](#prerequisites): Se não tiver definido a variável ambiente do sistema, pode encontrar a localização clicando no botão.
    - Basta escolher uma das duas opções e não são necessárias no MacOS e no Linux.

1. Também pode definir a configuração manualmente antes de realizar a execução local e o depurado local. Na imagem anterior, selecione**+** o sinal de mais ( ). Em seguida, selecione a Apache Spark na opção **HDInsight.** Introduza informações para **Nome**, **nome principal** da classe para guardar e, em seguida, clique no botão de execução local.

## <a name="perform-local-debugging"></a>Realizar depuração local

1. Abra o roteiro **SparkCore_wasbloTest,** definir breakpoints.

1. Clique no editor de scripts e, em seguida, selecione a opção **Debug '[Spark on HDInsight]XXX'** para executar depuração local.

## <a name="perform-remote-run"></a>Realizar execução remota

1. Navegar para **executar** > configurações de**edição...**. A partir deste menu, pode criar ou editar as configurações para depuração remota.

1. Na caixa de diálogo **'Configurações Executar/Depuração',** selecione o sinal de mais ().**+** Em seguida, selecione a Apache Spark na opção **HDInsight.**

   ![Intellij Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Mude para **Executar remotamente no** separador Cluster. Introduza informações para **Nome,** **cluster de faíscas**e **nome principal da classe**. Em seguida, clique na **configuração avançada (Depuração Remota)**. As nossas ferramentas suportam o depurado com **executores.** Os **numExectors,** o valor padrão é 5. É melhor não se fixar acima de 3.

   ![Configurações de depuração intellij Run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **Deconfiguração Avançada (Depuração Remota),** selecione **Ativar depuração remota spark**. Introduza o nome de utilizador SSH e introduza uma palavra-passe ou utilize um ficheiro de chave privado. Se quiser efetuar um depurado remoto, tem de o definir. Não há necessidade de o definir se quiser apenas usar uma corrida remota.

   ![Configuração Avançada Intellij permite depuração remota de faísca](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A configuração é agora guardada com o nome que forneceu. Para visualizar os detalhes da configuração, selecione o nome de configuração. Para efazer alterações, selecione **Configurações de Edição**.

1. Depois de completar as definições de configurações, pode executar o projeto contra o cluster remoto ou efetuar depuração remota.

   ![Botão de execução remoto de spark remote spark remote de intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Clique no botão **Desligar** que os registos de submissão não aparecem no painel esquerdo. No entanto, continua a correr na parte de trás.

   ![Resultado remoto de spark de inbug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Realizar depuração remota

1. Configurar pontos de rutura e, em seguida, clique no ícone **de depuração remoto.** A diferença com a submissão remota é que o nome de utilizador/senha sSH precisa de ser configurado.

   ![Ícone de depuração de spark de faísca remota intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atinge o ponto de rutura, vê-se um separador **condutor** e dois separadores **executor** no painel **do Debugger.** Selecione o ícone do **Programa Currículo** para continuar a executar o código, que depois atinge o próximo ponto de rutura. Tem de mudar para o **separador Executor** correto para encontrar o executor do alvo para depurar. Pode visualizar os registos de execução no separador **consolacorrespondente.**

   ![Intellij Debug Remote Spark Job Debugging tab](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Realizar depuração remota e fixação de insetos

1. Configurar dois pontos de rutura e, em seguida, selecionar o ícone **Debug** para iniciar o processo de depuração remoto.

1. O código para no primeiro ponto de rutura, e o parâmetro e a informação variável são mostrados no painel **de Variáveis.**

1. Selecione o ícone do **Programa Currículo** para continuar. O código para no segundo ponto. A exceção é apanhada como esperado.

   ![Erro de lançamento de spark remoto de Intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecione novamente o ícone do **Programa Currículo.** A janela **HDInsight Spark Submission** apresenta um erro de "execução de trabalho falhado".

   ![Submissão de erro de trabalho de faísca remota intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Para atualizar dinamicamente o valor variável utilizando a capacidade de depuração intelliJ, selecione **Debug** novamente. O painel **de Variáveis** aparece novamente.

1. Clique no alvo no separador **Debug** e, em seguida, selecione **Definir Valor**. Em seguida, insira um novo valor para a variável. Em seguida, selecione **Entrar** para guardar o valor.

   ![Intellij Debug Remote Spark Job definir valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecione o ícone do **Programa Currículo** para continuar a executar o programa. Desta vez, nenhuma exceção é apanhada. Pode ver que o projeto funciona com sucesso sem exceções.

   ![Intellij Debug Remote Spark Job sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
