---
title: Submeta trabalhos de R Tools for Visual Studio - Azure HDInsight
description: Envie trabalhos R da sua máquina local do Estúdio Visual para um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435257"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Submeter tarefas a partir das Ferramentas R para o Visual Studio

[R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) é uma extensão gratuita e de código aberto para as edições comunitárias (gratuitas), profissionais e empresariais do Visual Studio [2017](https://www.visualstudio.com/downloads/), e [visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) ou superior. O RTVS não está disponível para [o Visual Studio 2019.](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)

A RTVS melhora o seu fluxo de trabalho R oferecendo ferramentas como a [janela R Interactive](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (conclusão de código), [visualização](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) do enredo através de bibliotecas R como ggplot2 e ggviz, [depuração](https://docs.microsoft.com/visualstudio/rtvs/debugging)de código R, e muito mais.

## <a name="set-up-your-environment"></a>Configurar o ambiente

1. Instale [ferramentas R para estúdio visual](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalação de RTVS no Estúdio Visual 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecione a carga de trabalho da *ciência dos dados e aplicações analíticas* e, em seguida, selecione o suporte de **idioma R,** **suporte de tempo de execução para desenvolvimento r**e opções do Cliente Microsoft **R.**

3. Precisa de chaves públicas e privadas para autenticação SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale o [Servidor ML](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na sua máquina. O Servidor ML [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) `RxSpark` fornece as e funções.

5. Instale o [PuTTY](https://www.putty.org/) para fornecer `RevoScaleR` um contexto de cálculo para executar funções do seu cliente local para o seu cluster HDInsight.

6. Tem a opção de aplicar as Definições de Ciência de Dados ao seu ambiente de Estúdio Visual, que fornece um novo layout para o seu espaço de trabalho para as ferramentas R.
   1. Para guardar as definições atuais do Estúdio Visual, utilize o comando **Tools > Import e Export Settings,** e, em seguida, selecione as definições de ambiente **selecionadas para exportação** e especifique um nome de ficheiro. Para restaurar essas definições, utilize o mesmo comando e selecione definições de **ambiente selecionadas import**.

   2. Vá ao item do menu **R Tools** e, em seguida, selecione Definições de Ciência de **Dados...**.

       ![Definições de ciência de dados do estúdio visual](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Utilizando a abordagem no passo 1, também pode guardar e restaurar o seu layout personalizado de cientista de dados, em vez de repetir o comando de Definições de Ciência de **Dados.**

## <a name="execute-local-r-methods"></a>Executar métodos R locais

1. Crie o seu cluster hDInsight ML Services.
2. Instale a [extensão RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Descarregue o ficheiro zip das [amostras.](https://github.com/Microsoft/RTVS-docs/archive/master.zip)
4. Aberto `examples/Examples.sln` para lançar a solução no Estúdio Visual.
5. Abra `1-Getting Started with R.R` o ficheiro `A first look at R` na pasta da solução.
6. A partir da parte superior do ficheiro, prima Ctrl+Enter para enviar cada linha, uma de cada vez, para a janela R Interactive. Algumas linhas podem demorar algum tempo enquanto instalam pacotes.
    * Em alternativa, pode selecionar todas as linhas do ficheiro R (Ctrl+A), em seguida, executar todas (Ctrl+Enter), ou selecionar o ícone Executar Interactive na barra de ferramentas.

        ![Estúdio Visual executar interativo](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Depois de executar todas as linhas do script, você deve ver uma saída semelhante a esta:

    ![Ferramentas R do espaço de trabalho do Estúdio Visual](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Submeta empregos a um cluster de serviços HDInsight ML

Utilizando um Microsoft ML Server/Microsoft R Client a partir de um computador Windows equipado `RevoScaleR` com PuTTY, pode criar um contexto de computação que executará funções distribuídas do seu cliente local para o seu cluster HDInsight. Utilize `RxSpark` para criar o contexto da computação, especificando o seu nome de utilizador, o nó de borda do cluster Apache Hadoop, os interruptores SSH, e assim por diante.

1. O endereço do nó de borda `CLUSTERNAME-ed-ssh.azurehdinsight.net` ml `CLUSTERNAME` Services no HDInsight é onde está o nome do seu cluster ml Services.

1. Colhe o seguinte código na janela R Interactive no Estúdio Visual, alterando os valores das variáveis de configuração para combinar com o seu ambiente.

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

1. Executar os seguintes comandos na janela R Interactive:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ![Execução](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) de comando rx bem-sucedida a
1. Verifique se `rxHadoopCopy` o ficheiro `people.json` copiou com sucesso a pasta `/user/RevoShare/newUser` de dados da exemplo para a pasta recém-criada:

    1. A partir do painel de cluster hDInsight ML Services em Azure, selecione contas de **armazenamento** do menu à esquerda.

        ![Contas de armazenamento Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecione a conta de armazenamento predefinida para o seu cluster, tocando nota do nome do recipiente/diretório.

    3. Selecione **Recipientes** do menu à esquerda no painel da sua conta de armazenamento.

        ![Recipientes de armazenamento Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecione o nome do recipiente do seu cluster, navegue para a pasta do **utilizador** (pode ter de clicar mais na *carregar na* parte inferior da lista), em seguida, selecione *RevoShare*, em **seguida, novoUser**. O `people.json` ficheiro deve ser `newUser` apresentado na pasta.

        ![Localização da pasta de ficheiros copiada HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Depois de terminar de usar o atual contexto Apache Spark, deve pará-lo. Não se pode executar vários contextos ao mesmo tempo.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Opções de contexto computacional para serviços ML no HDInsight](r-server-compute-contexts.md)
* [Combinar ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo das previsões de atraso seleto de voos aéreos.
