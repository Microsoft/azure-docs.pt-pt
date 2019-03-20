---
title: Submeter tarefas a partir das ferramentas R para Visual Studio - Azure HDInsight
description: Submeta tarefas de R a partir do seu computador local do Visual Studio para um cluster do HDInsight.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 3165adf09281f4ad35b77c2e0fbb89e40c04c631
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120963"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Submeter tarefas a partir das Ferramentas R para o Visual Studio

[R Tools para Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) é uma extensão gratuita e de código-fonte aberto para a Comunidade (gratuita), Professional e Enterprise editions de ambos [Visual Studio 2017](https://www.visualstudio.com/downloads/), e [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)ou superior.

RTVS melhora o seu fluxo de trabalho do R, oferecendo ferramentas como o [R Interaktivní okno](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), o intellisense (preenchimento de código), [plot visualização](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) através de bibliotecas de R como ggplot2 e ggviz, [Depuração de código de R](https://docs.microsoft.com/visualstudio/rtvs/debugging)e muito mais.

## <a name="set-up-your-environment"></a>Configurar o ambiente

1. Instale [R Tools for Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalar o RTVS no Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecione o *ciência de dados e aplicativos analíticos* carga de trabalho, em seguida, selecione a **suporte de linguagem R**, **suporte de tempo de execução para o desenvolvimento de R**, e  **Microsoft R Client** opções.

3. Tem de ter as chaves públicas e privadas para a autenticação de SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) no seu computador. ML Server fornece a [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) e `RxSpark` funções.

5. Instale [PuTTY](https://www.putty.org/) para fornecer um contexto de computação para executar `RevoScaleR` funções a partir do seu cliente local no seu cluster do HDInsight.

6. Tem a opção para aplicar as definições de ciência de dados para o ambiente do Visual Studio, que fornece um novo esquema para a área de trabalho para as ferramentas de R.
   1. Para guardar as definições atuais do Visual Studio, utilize o **Tools > Import and Export Settings** comando, em seguida, selecione **Export selected definições de ambiente** e especifique um nome de ficheiro. Para restaurar essas definições, utilize o comando mesmo e selecione **Import selected definições de ambiente**.

   2. Vá para o **R Tools** menu item, em seguida, selecione **definições de ciência de dados...** .

       ![Definições de ciência de dados...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Usando a abordagem no passo 1, também pode guardar e restaurar o layout de cientista de dados personalizados, em vez de repetir a **definições de ciência de dados** comando.

## <a name="execute-local-r-methods"></a>Executar métodos locais do R

1. Criar sua [cluster de HDInsight ML serviços](r-server-get-started.md).
2. Instalar o [extensão RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Transfira o [exemplos de arquivos zip](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar a solução no Visual Studio.
5. Abra o `1-Getting Started with R.R` de ficheiros a `A first look at R` pasta da solução.
6. A iniciar na parte superior do arquivo, pressione Ctrl + Enter enviar a cada linha, um de cada vez, na janela interativa de R. Algumas linhas podem demorar algum tempo, pacotes de instalação.
    * Em alternativa, pode selecionar todas as linhas no ficheiro de R (Ctrl + A), em seguida, executar todas as (Ctrl + Enter) ou selecione o ícone de executar interativo na barra de ferramentas.
        ![Executar em interativo](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Depois de executar todas as linhas no script, deverá ver um resultado semelhante ao seguinte:

    ![Definições de ciência de dados...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Submeter tarefas para um cluster do HDInsight serviços de ML

Usando um Microsoft ML Server/Microsoft R Client a partir de um computador Windows equipado com PuTTY, pode criar um contexto de cálculo que executará distribuído `RevoScaleR` funções a partir do seu cliente local no seu cluster do HDInsight. Utilize `RxSpark` para criar o contexto de computação, especificando seu nome de utilizador, nó de extremidade do cluster do Apache Hadoop, comutadores SSH e assim por diante.

1. Para localizar o nome de anfitrião do nó de extremidade, abrir o painel de cluster de HDInsight ML serviços no Azure, em seguida, selecione **Secure Shell (SSH)** no menu superior do painel de descrição geral.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Copiar o **nome de anfitrião do nó de extremidade** valor.

    ![Nome de anfitrião do nó periférico](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Cole o seguinte código na janela interativa do R no Visual Studio, alterar os valores das variáveis de configuração para corresponder ao seu ambiente.

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
    
    ![Definir o contexto de Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Execute os seguintes comandos na janela interativa do R:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ![Execução do comando rx com êxito](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Certifique-se de que o `rxHadoopCopy` copiado com êxito o `people.json` ficheiro a partir da pasta de dados de exemplo para o recém-criado `/user/RevoShare/newUser` pasta:

    1. No seu painel de cluster do HDInsight ML serviços no Azure, selecione **contas de armazenamento** no menu esquerdo.

        ![Contas de armazenamento](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Selecione a conta de armazenamento predefinida para o seu cluster, tornando a nota do nome de diretório do contentor.

    3. Selecione **contentores** no menu esquerdo no seu painel de conta de armazenamento.

        ![Contentores](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Selecione o nome do contentor do seu cluster, navegue para o **utilizador** pasta (poderá ter de clicar em *carregar mais* na parte inferior da lista), em seguida, selecione *RevoShare*, em seguida, no **newUser**. O `people.json` ficheiro deve ser apresentado no `newUser` pasta.

        ![Ficheiro copiado](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Depois de terminar de usar o contexto atual do Apache Spark, tem de o interromper. Não é possível executar vários contextos de uma só vez.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Opções de contexto para os serviços de ML no HDInsight de computação](r-server-compute-contexts.md)
* [A combinação de ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo de previsões de atrasos de voos de companhia aérea.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
