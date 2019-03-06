---
title: Introdução aos Serviços de ML no HDInsight - Azure
description: Saiba como criar um Apache Spark num cluster do HDInsight que inclui os Serviços ML e submeter um script R no cluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: dce345c39918e7471531710051797c848a3c7cde
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444077"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Introdução aos Serviços ML no Azure HDInsight

O Azure HDInsight permite-lhe criar um cluster dos Serviços ML. Esta opção permite que scripts R utilizem [Apache Spark](https://spark.apache.org/) e [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) para executar cálculos distribuídos. Neste artigo, vai aprender a criar um cluster do Serviço ML no HDInsight e como executar um script R que demonstre a utilização do Spark para cálculos de R distribuídos.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**: Antes de começar este tutorial, tem de ter uma subscrição do Azure. Para obter mais informações, veja [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obter a avaliação gratuita do Microsoft Azure).
* **Um cliente Secure Shell (SSH)**: Um cliente SSH é utilizado para ligar ao HDInsight cluster e executar comandos diretamente no cluster remotamente. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Criar o cluster com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue para **+ criar um recurso** > **análise** > **HDInsight**.

3. Em **Informações Básicas**, introduza as seguintes informações:

    * **Nome do cluster**: O nome do cluster do HDInsight.
    * **Subscrição**: Selecione a subscrição a utilizar.
    * **Nome de início de sessão do cluster** e **palavra-passe de início de sessão do Cluster**: O início de sessão quando aceder ao cluster através de HTTPS. Utilize estas credenciais para aceder aos serviços como o Apache Ambari Web UI ou a REST API.
    * **Secure Shell (SSH) nome de utilizador**: O início de sessão utilizado ao aceder ao cluster através de SSH. Por predefinição, a palavra-passe é igual à palavra-passe de início de sessão do cluster.
    * **Grupo de recursos**: O grupo de recursos em que o cluster é criado.
    * **Localização**: A região do Azure em que o cluster é criado.

        ![Detalhes básicos do cluster](./media/r-server-get-started/clustername.png)

4. Selecione **Tipo de cluster**e, em seguida, defina os seguintes valores na secção **Configuração de cluster**:

    * **Tipo de cluster**: Serviços ML

    * **Sistema operativo**: Linux

    * **Versão**: Servidor de ML 9.3 (HDI 3.6). As notas da versão do ML Server 9.3 estão disponíveis no [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **Edição de Comunidade do R Studio para o servidor de ML**: Este IDE baseado no browser está instalado por predefinição no nó de extremidade. Desmarque a caixa de verificação, se preferir não instalá-lo. Se optar por instalar, o URL para aceder ao início de sessão do RStudio Server está disponível num painel de aplicação do portal do seu cluster, depois de ser criado.

        ![Detalhes básicos do cluster](./media/r-server-get-started/clustertypeconfig.png)

4. Depois de selecionar o tipo de cluster, utilize o botão __Selecionar__ para definir o tipo de cluster. Em seguida, utilize o botão __Seguinte__ para concluir a configuração básica.

5. Na secção **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para seguir os passos neste documento, deixe os outros campos nesta secção com os valores predefinidos. Utilize o botão __Seguinte__ para guardar a configuração do armazenamento.

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Na secção **Resumo**, reveja a configuração do cluster. Utilize as ligações __Editar__ para alterar quaisquer definições que estejam incorretas. Por fim, clique no botão __Criar__ para criar o cluster.

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > A criação do cluster pode demorar até 20 minutos.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

Se optar por instalar o R Studio Server Community Edition como parte do cluster do HDInsight, pode aceder ao início de sessão do RStudio com um dos seguintes dois métodos:

* **Opção 1** - aceda ao URL seguinte (em que **CLUSTERNAME** é o nome do cluster dos Serviços ML que criou):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opção 2** -utilizar o portal do Azure.
No portal:
  1. Selecione **todos os serviços** no menu à esquerda.
  2. Sob **ANALYTICS**, selecione **clusters do HDInsight**.
  3. Selecione o nome de cluster a partir da **clusters do HDInsight** página.
  4. Partir **dashboards de serviços de ML**, selecione **R Studio server**. 

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]  
   > Independentemente do método utilizado, vai ter de se autenticar duas vezes da primeira vez que iniciar sessão.  No primeiro aviso de autenticação, indique o *userID* e a *palavra-passe de administrador do cluster*. No segundo aviso de autenticação, indique o *userID* e a *palavra-passe de administrador do SSH*. Logons subseqüentes necessitam apenas das credenciais SSH.

Assim que estiver ligado, o ecrã deve assemelhar-se à captura de ecrã seguinte:

![Ligar ao RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Executar uma tarefa de exemplo

Pode submeter uma tarefa através das funções ScaleR. Eis um exemplo dos comandos utilizados para executar uma tarefa:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Ligar ao nó periférico do cluster

Nesta secção, saiba como ligar ao nó periférico de um cluster HDInsight dos Serviços ML através de SSH. Para familiaridade sobre a utilização de SSH, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

O comando SSH para ligar ao nó periférico do cluster dos Serviços ML é:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Para localizar o comando SSH para o cluster, no Portal do Azure, clique no nome do cluster, clique em **Início de sessão de SSH + Cluster** e, em seguida, em **Hostname**, selecione o nó periférico. São apresentadas as informações do Ponto Final de SSH relativas ao nó de extremidade.

![Imagem do Ponto Final de SSH do nó de extremidade](./media/r-server-get-started/sshendpoint.png)

Se utilizou uma palavra-passe para proteger a sua conta de utilizador do SSH, é solicitado que a introduza. Se utilizou uma chave pública, poderá ter de utilizar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Quando estiver ligado, vai deparar-se com uma mensagem semelhante à seguinte:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Utilizar a consola de R

1. Na sessão SSH, utilize o comando seguinte para iniciar a consola do R:  

        R

2. Deverá ver um resultado com a versão dos Serviços ML, além de outras informações.
    
3. Na linha de comandos `>`, pode introduzir o código R. Os Serviços ML no HDInsight incluem pacotes que lhe permitem interagir facilmente com o Hadoop e executar cálculos distribuídos. Por exemplo, utilize o comando seguinte para ver a raiz do sistema de ficheiros predefinido do cluster do HDInsight:

        rxHadoopListFiles("/")

4. Também pode utilizar o endereçamento de estilo WASB.

        rxHadoopListFiles("wasb:///")

5. Para sair da consola do R, utilize o seguinte comando:

        quit()

## <a name="automated-cluster-creation"></a>Criação de clusters automatizada

Pode automatizar a criação de um cluster dos Serviços ML para HDInsight com o SDK e o PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Para criar um cluster dos Serviços ML com o SDK do .NET, veja [Criar clusters baseados em Linux no HDInsight com o SDK do .NET.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Para criar um cluster dos Serviços ML com o PowerShell, leia o artigo sobre [Criar clusters do HDInsight com o Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a criar um novo cluster de serviços de ML no Azure HDInsight e as noções básicas da utilização da consola de R numa sessão SSH. Os artigos seguintes explicam outras formas de gerir e trabalhar com os Serviços ML no HDInsight:

* [Submeter tarefas a partir das Ferramentas R para o Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Gerir cluster dos Serviços ML no HDInsight](r-server-hdinsight-manage.md)
* [Operacionalizar cluster dos Serviços ML no HDInsight](r-server-operationalize.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)
