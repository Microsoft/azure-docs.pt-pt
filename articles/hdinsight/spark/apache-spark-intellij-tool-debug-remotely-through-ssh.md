---
title: 'Azure Toolkit para IntelliJ: Debug Spark apps com SSH - HDInsight'
description: Orientações passo a passo sobre como usar ferramentas HDInsight em Azure Toolkit para IntelliJ para depurar as aplicações remotamente em clusters HDInsight através de SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 421993ac4aaba551b6fcbd002783d44559ce377d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995340"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Aplicações Debug Apache Spark em um cluster HDInsight com Azure Toolkit para IntelliJ através de SSH

Este artigo fornece orientações passo a passo sobre como usar ferramentas HDInsight em [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij) depurar as aplicações remotamente num cluster HDInsight. Para depurar o seu projeto, também pode ver as [aplicações Debug HDInsight Spark com Azure Toolkit para vídeo IntelliJ.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache.](../spark/apache-spark-jupyter-spark-sql-use-portal.md)

* Para os utilizadores do Windows: Enquanto estiver a executar a aplicação local Spark Scala num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe falta no Windows.

    Para resolver este erro, faça o download [Winutils.exe](https://github.com/steveloughran/winutils) para um local como **C:\WinUtils\bin**. Em seguida, adicione a variável ambiental **HADOOP_HOME**, e desave o valor da variável para **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (A edição comunitária é gratuita).).

* [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Plugin scala para IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Criar uma aplicação Spark Scala

1. Inicie a Ideia IntelliJ e selecione **Criar Novo Projeto** para abrir a janela New **Project.**

1. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.

1. Selecione **Spark Project com amostras (Scala)** da janela principal.

1. A partir da lista de drop-down da **ferramenta Build,** selecione uma das seguintes:

    * **Maven** para o suporte de assistente de criação de projeto Scala.
    * **SBT** para gerir as dependências e construção para o projeto Scala.

     ![Intellij Criar Novo Projeto Faísca](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecione **Seguinte**.

1. Na próxima janela **do Novo Projeto,** forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome do projeto|Insira um nome. Este passeio através de `myApp` usos.|
    |Localização do projeto|Insira o local pretendido para salvar o seu projeto.|
    |Projeto SDK|Se estiver em branco, selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x.** Este exemplo utiliza **a Faísca 2.3.0 (Scala 2.11.8)**.|

   ![Intellij New Project seleciona versão Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecione **Concluir**. Pode levar alguns minutos até que o projeto fique disponível. Cuidado com o canto inferior direito para progredir.

1. Expanda o seu projeto e navegue para a amostra principal **de**  >  **main**  >  **scala**  >  do SRC.**sample** Clique **duas vezes SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Realizar corrida local

1. A partir **do** SparkCore_WasbIOTest script, clique com o direito no editor de script e, em seguida, selecione a opção Executar **'SparkCore_WasbIOTest'** para executar a execução local.

1. Uma vez concluída a execução local, pode ver o ficheiro de saída guardar para o seu atual projeto de **dados**  >  **__de exploradores predefinidos__**.

    ![Resultado da corrida local do Projeto Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. As nossas ferramentas definiram automaticamente a configuração de execução local padrão quando executa a execução local e o depuração local. Abra a configuração **[Spark on HDInsight] XXX** no canto superior direito, pode ver o **[Spark on HDInsight]XXX** já criado sob **Apache Spark em HDInsight**. Mude para o **separador LocalMente Executar.**

    ![Intellij Run depurar configurações locais run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Variáveis ambientais](#prerequisites): Se já definir a variável do ambiente do sistema **HADOOP_HOME** a **C:\WinUtils,** pode detetar automaticamente que não é necessário adicionar manualmente.
    - [WinUtils.exe Localização](#prerequisites): Se não tiver definido a variável ambiente do sistema, pode encontrar a localização clicando no botão.
    - Basta escolher uma das duas opções e não são necessárias no MacOS e linux.

1. Também pode definir a configuração manualmente antes de executar a execução local e o depurg local. Na imagem anterior, selecione o sinal mais **+** (). Em seguida, selecione a faísca Apache na opção **HDInsight.** Introduza informações para **Nome,** **nome da classe principal** para guardar e, em seguida, clique no botão de execução local.

## <a name="perform-local-debugging"></a>Realizar depuragem local

1. Abra **o** SparkCore_wasbloTest script, definir pontos de rutura.

1. Clique com o botão direito no editor de script e, em seguida, selecione a opção **Debug '[Spark on HDInsight]XXX'** para executar a depuração local.

## <a name="perform-remote-run"></a>Executar execução remota

1. Navegue para **executar**  >  **configurações de edição...**. A partir deste menu, pode criar ou editar as configurações para depurar remotamente.

1. Na caixa de diálogo **de configurações Run/Debug,** selecione o sinal mais **+** (). Em seguida, selecione a faísca Apache na opção **HDInsight.**

   ![Intellij Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Mude para **executar remotamente no separador Cluster.** Introduza informações para **Nome,** **agrupamento de faíscas** e **nome da classe principal.** Em seguida, clique na **configuração avançada (Depuragem remota)**. As nossas ferramentas suportam o depurar com **os Executores.** Os **numExectors,** o valor predefinido é 5. É melhor não colocar mais de 3.

   ![Configurações de depurações intellij Run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **de Configuração Avançada (Depuragem Remota),** selecione **Ativar o depurg remoto Depurador Spark**. Introduza o nome de utilizador SSH e, em seguida, introduza uma palavra-passe ou utilize um ficheiro de chave privada. Se quiser realizar depurar remotamente, tem de o definir. Não é necessário defini-lo se quiser apenas utilizar o funcional remoto.

   ![Configuração Avançada intellij permite depurar depurg remoto de faísca](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A configuração é agora guardada com o nome que forneceu. Para visualizar os detalhes da configuração, selecione o nome de configuração. Para escoar as **alterações, selecione Configurações de Edição**.

1. Depois de completar as definições de configurações, pode executar o projeto contra o cluster remoto ou executar depuração remota.

   ![Botão de funcionação remota de intellij Debug Debug Remote Spark Job](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Clique no botão **Desligar** para que os registos de submissão não apareçam no painel esquerdo. No entanto, ainda está a correr no backend.

   ![Resultado remoto de execução de Intellij Debug Debug Remote Spark Job](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Realizar depuragem remota

1. Configurar pontos de rutura e, em seguida, clicar no ícone **de depurar remoto.** A diferença com a submissão remota é que o nome de utilizador/palavra-passe SSH precisa de ser configurado.

   ![Ícone de depurar faísca remota intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atingir o ponto de rutura, vê-se um **separador do Controlador** e dois separadores **executor** no painel **de Debugger.** Selecione o ícone **do Programa retomar** para continuar a executar o código, que depois atinge o ponto de rutura seguinte. Tem de mudar para o **separador executor** correto para encontrar o executor alvo para depurar. Pode visualizar os registos de execução no **separador Consola** correspondente.

   ![Intellij Debug Remote Spark Job Debugging](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Executar depuração remota e fixação de bugs

1. Configurar dois pontos de rutura e, em seguida, selecione o ícone **Debug** para iniciar o processo de depuração remota.

1. O código para no primeiro ponto de rutura, e o parâmetro e a informação variável são mostrados no painel **de variáveis.**

1. Selecione o ícone **do Programa retomar** para continuar. O código para no segundo ponto. A exceção é apanhada como esperado.

   ![Erro de lançamento de faísca remota intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecione novamente o ícone **do Programa retomar.** A janela **de submissão de faíscas HDInsight** apresenta um erro de "execução de trabalho falhado".

   ![Submissão de Erro de Trabalho de Faísca Remota Intellij Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Para atualizar dinamicamente o valor variável utilizando a capacidade de depuração IntelliJ, selecione **Debug** novamente. O painel de **variáveis** aparece novamente.

1. Clique com o botão direito no separador **Debug** e, em seguida, selecione **'Valor'** de Conjunto . Em seguida, introduza um novo valor para a variável. Em seguida, **selecione Enter** para guardar o valor.

   ![Intellij Debug Remote Spark Job valor definido](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecione o ícone **do Programa retomar** para continuar a executar o programa. Desta vez, nenhuma exceção é apanhada. Pode ver que o projeto funciona com sucesso sem exceções.

   ![Intellij Debug Remote Spark Job sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar Aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depurador remoto (vídeo): [Use o Azure Toolkit para IntelliJ para depurar as aplicações Apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realize análise de dados interativas utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](./apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit para IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações apache spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize ferramentas HDInsight em Azure Toolkit para eclipse para criar aplicações Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o caderno Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)