---
title: Enviar trabalhos do Ferramentas do R para Visual Studio-HDInsight do Azure
description: Envie trabalhos do R do seu computador do Visual Studio local para um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435257"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Submeter tarefas a partir das Ferramentas R para o Visual Studio

O [Ferramentas do R para Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) é uma extensão de software livre gratuita para as edições da Comunidade (gratuita), Professional e Enterprise do [Visual Studio 2017](https://www.visualstudio.com/downloads/)e do [Visual Studio 2015 atualização 3](https://go.microsoft.com/fwlink/?LinkId=691129) ou superior. O RTVS não está disponível para o [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

O RTVS aprimora o seu fluxo de trabalho de R oferecendo ferramentas como a repl ( [R interativo Window](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) ), o IntelliSense (auto-completar de código), a [Visualização](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) de gráficos por meio de bibliotecas de R, como ggplot2 e ggviz, [depuração de código R](https://docs.microsoft.com/visualstudio/rtvs/debugging)e muito mais.

## <a name="set-up-your-environment"></a>Configurar o ambiente

1. Instale o [Ferramentas do R para Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalando o RTVS no Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecione a carga de trabalho *ciência de dados e aplicativos analíticos e* , em seguida, selecione o **suporte à linguagem r**, **suporte ao tempo de execução para desenvolvimento R**e opções de **Microsoft R Client** .

3. Você precisa ter chaves públicas e privadas para autenticação SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale o [ml Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) em seu computador. ML Server fornece as funções [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) e `RxSpark`.

5. Instale a saída para fornecer um contexto de [computação para executar](https://www.putty.org/) `RevoScaleR` funções do seu cliente local para o cluster HDInsight.

6. Você tem a opção de aplicar o Configurações da Ciência de Dados ao seu ambiente do Visual Studio, que fornece um novo layout para o seu espaço de trabalho para as ferramentas do R.
   1. Para salvar as configurações atuais do Visual Studio, use o comando **ferramentas > importar e exportar configurações** , em seguida, selecione **exportar configurações de ambiente selecionadas** e especifique um nome de arquivo. Para restaurar essas configurações, use o mesmo comando e selecione **Importar configurações de ambiente selecionadas**.

   2. Vá para o item de menu **Ferramentas do R** e, em seguida, selecione **configurações da ciência de dados...** .

       ![Configurações da Ciência de Dados do Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Usando a abordagem na etapa 1, você também pode salvar e restaurar seu layout de cientista de dados personalizado, em vez de repetir o comando **configurações da ciência de dados** .

## <a name="execute-local-r-methods"></a>Executar métodos R locais

1. Crie seu cluster de serviços do HDInsight ML.
2. Instale a [extensão RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Baixe o [arquivo zip de exemplos](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar a solução no Visual Studio.
5. Abra o arquivo `1-Getting Started with R.R` na pasta `A first look at R` solução.
6. Iniciando na parte superior do arquivo, pressione Ctrl + Enter para enviar cada linha, uma de cada vez, para a janela de R Interativo. Algumas linhas podem levar algum tempo à medida que instalam pacotes.
    * Como alternativa, você pode selecionar todas as linhas no arquivo do R (Ctrl + A) e, em seguida, executar todos (Ctrl + Enter) ou selecionar o ícone executar interativo na barra de ferramentas.

        ![Execução interativa do Visual Studio](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Depois de executar todas as linhas no script, você verá uma saída semelhante a esta:

    ![Ferramentas de R do espaço de trabalho do Visual Studio](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Enviar trabalhos para um cluster de serviços de am do HDInsight

Usando um Microsoft ML Server/Microsoft R Client de um computador com Windows equipado com recriação, você pode criar um contexto de computação que executará funções de `RevoScaleR` distribuídas do seu cliente local para o cluster HDInsight. Use `RxSpark` para criar o contexto de computação, especificando seu nome de usuário, o nó de borda do cluster Apache Hadoop, comutadores SSH e assim por diante.

1. O endereço do nó de borda dos serviços ML no HDInsight é `CLUSTERNAME-ed-ssh.azurehdinsight.net` em que `CLUSTERNAME` é o nome do seu cluster de serviços am.

1. Cole o código a seguir na janela R Interativo no Visual Studio, alterando os valores das variáveis de instalação para corresponder ao seu ambiente.

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

   ![Apache Spark Configurando o contexto](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Execute os seguintes comandos na janela R Interativo:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ![execução bem-sucedida do comando RX](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) um
1. Verifique se a `rxHadoopCopy` copiou com êxito o arquivo de `people.json` da pasta de dados de exemplo para a pasta de `/user/RevoShare/newUser` recém-criada:

    1. No painel de cluster de serviços de am do HDInsight no Azure, selecione **contas de armazenamento** no menu à esquerda.

        ![Contas de armazenamento do Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecione a conta de armazenamento padrão para o cluster, anotando o nome do contêiner/diretório.

    3. Selecione **contêineres** no menu à esquerda no painel da sua conta de armazenamento.

        ![Contêineres de armazenamento do Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecione o nome do contêiner do cluster, navegue até a pasta do **usuário** (talvez seja necessário clicar em *carregar mais* na parte inferior da lista), em seguida, selecione *RevoShare*, em seguida, **newuser**. O arquivo de `people.json` deve ser exibido na pasta `newUser`.

        ![Local da pasta de arquivos copiados do HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Depois de terminar de usar o contexto de Apache Spark atual, você deverá interrompê-lo. Você não pode executar vários contextos de uma vez.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Opções de contexto de computação para serviços de ML no HDInsight](r-server-compute-contexts.md)
* A [combinação do scaler e do sparkr](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo de previsões de atraso de voo aérea.
