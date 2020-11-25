---
title: Enviar empregos de R Tools for Visual Studio - Azure HDInsight
description: Submeta trabalhos R da sua máquina de Estúdio Visual local para um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 08426c74b26c18b15466578d9921520da1e9c923
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993793"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Submeter tarefas a partir das Ferramentas R para o Visual Studio

[R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) é uma extensão gratuita e aberta para as edições comunitárias (gratuitas), profissionais e empresariais de ambos o Visual Studio [2017](https://www.visualstudio.com/downloads/)e [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) ou superior. O RTVS não está disponível para [o Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

O RTVS melhora o seu fluxo de trabalho R oferecendo ferramentas como a [janela R Interactive](/visualstudio/rtvs/interactive-repl) (REPL), intellisense (conclusão de código), [visualização de enredo](/visualstudio/rtvs/visualizing-data) através de bibliotecas R como ggplot2 e ggviz, [depuração de código R,](/visualstudio/rtvs/debugging)e muito mais.

## <a name="set-up-your-environment"></a>Configurar o seu ambiente

1. Instale [ferramentas R para Estúdio Visual](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalação RTVS no Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecione a carga de trabalho da ciência dos *dados e aplicações analíticas,* em seguida, selecione o **suporte de linguagem R,** **suporte de tempo de execução para desenvolvimento R** e opções do Cliente Microsoft **R.**

3. Precisa de chaves públicas e privadas para a autenticação do SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale [o Servidor ML](/previous-versions/machine-learning-server/install/r-server-install-windows) na sua máquina. O ML Server fornece as [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) funções e `RxSpark` as funções.

5. Instale [o PuTTY](https://www.putty.org/) para fornecer um contexto computacional para executar `RevoScaleR` funções do seu cliente local para o seu cluster HDInsight.

6. Tem a opção de aplicar as Definições de Ciência de Dados ao seu ambiente de Estúdio Visual, que fornece um novo layout para o seu espaço de trabalho para as ferramentas R.
   1. Para guardar as definições atuais do Estúdio Visual, utilize o comando **Ferramentas > De importação e Exportação,** selecione **As definições de ambiente selecionadas para exportar** e especifique um nome de ficheiro. Para restaurar estas definições, utilize o mesmo comando e selecione **Import selete De ambiente selecionado**.

   2. Vá ao item do menu **R Tools** e, em seguida, selecione **Definições de Ciência de Dados...**.

       ![Definições de ciência de dados de estúdio visual](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Utilizando a abordagem no passo 1, também pode guardar e restaurar o seu layout personalizado de cientistas de dados, em vez de repetir o comando **de Definições de Ciência de Dados.**

## <a name="execute-local-r-methods"></a>Executar métodos R locais

1. Crie o seu cluster hdInsight ML Services.
2. Instale a [extensão RTVS](/visualstudio/rtvs/installation).
3. Descarregue o ficheiro zip de [amostras.](https://github.com/Microsoft/RTVS-docs/archive/master.zip)
4. Aberto `examples/Examples.sln` para lançar a solução no Visual Studio.
5. Abra o `1-Getting Started with R.R` ficheiro na pasta da `A first look at R` solução.
6. A partir da parte superior do ficheiro, prima Ctrl+Enter para enviar cada linha, uma de cada vez, para a janela R Interactive. Algumas linhas podem demorar algum tempo enquanto instalam pacotes.
    * Em alternativa, pode selecionar todas as linhas do ficheiro R (Ctrl+A), em seguida, executar todas (Ctrl+Enter) ou selecionar o ícone Executar Interactive na barra de ferramentas.

        ![Estúdio Visual executa interativo](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Depois de executar todas as linhas do script, você deve ver uma saída semelhante a esta:

    ![Ferramentas R do espaço de trabalho do Estúdio Visual](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Submeta empregos a um cluster de serviços ML HDInsight

Utilizando um Microsoft ML Server/Microsoft R Client a partir de um computador Windows equipado com PuTTY, pode criar um contexto computacional que executará `RevoScaleR` funções distribuídas do seu cliente local para o seu cluster HDInsight. Utilize `RxSpark` para criar o contexto computacional, especificando o seu nome de utilizador, o nó de borda do cluster Apache Hadoop, comutadores SSH e assim por diante.

1. O endereço de nó de borda ml services em HDInsight é `CLUSTERNAME-ed-ssh.azurehdinsight.net` onde está o nome do seu cluster `CLUSTERNAME` ML Services.

1. Cole o seguinte código na janela R Interactive em Visual Studio, alterando os valores das variáveis de configuração para combinar com o seu ambiente.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![faísca apache definindo o contexto](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Execute os seguintes comandos na janela R Interactive:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ![Execução de comando rx bem-sucedida ](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) a
1. Verifique se o `rxHadoopCopy` ficheiro copiou com sucesso `people.json` da pasta de dados de exemplo para a pasta recém-criada: `/user/RevoShare/newUser`

    1. A partir do painel de agrupamento hdInsight ML Services em Azure, selecione **as contas** de Armazenamento do menu à esquerda.

        ![Contas de armazenamento Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecione a conta de armazenamento predefinido para o seu cluster, fazendo nota do nome do contentor/diretório.

    3. Selecione **recipientes** do menu à esquerda no painel da sua conta de armazenamento.

        ![Recipientes de armazenamento Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecione o nome do seu cluster, navegue na pasta do **utilizador** (poderá ter de clicar *mais* na parte inferior da lista), depois selecione *RevoShare*, em seguida, **newUser**. O `people.json` ficheiro deve ser apresentado na `newUser` pasta.

        ![Localização da pasta de ficheiro copiada HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Depois de terminar de usar o contexto atual do Apache Spark, deve detê-lo. Não pode executar vários contextos ao mesmo tempo.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Opções de contexto computacional para serviços ML em HDInsight](r-server-compute-contexts.md)
* [A combinação da ScaleR e da SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo das previsões de atrasos de voo da companhia aérea.