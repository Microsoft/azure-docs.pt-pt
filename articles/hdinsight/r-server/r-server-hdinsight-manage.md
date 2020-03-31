---
title: Gerir o cluster de serviços ML no HDInsight - Azure
description: Saiba como gerir várias tarefas no cluster ml Services em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647735"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Gerir o cluster de serviços ML no Azure HDInsight

Neste artigo, aprende-se a gerir um cluster de serviços ML existente no Azure HDInsight para executar tarefas como adicionar vários utilizadores simultâneos, conectando-se remotamente a um cluster de Serviços ML, alterando o contexto da computação, etc.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços ML no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **ml Services** for **Cluster type**.

* Um cliente Secure Shell (SSH): é utilizado um cliente SSH para ligar remotamente ao cluster do HDInsight e executar comandos diretamente no mesmo. Para mais informações, consulte [Use SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md). .

## <a name="enable-multiple-concurrent-users"></a>Permitir vários utilizadores em simultâneo

Pode ativar vários utilizadores simultâneos para o cluster ml Services no HDInsight, adicionando mais utilizadores para o nó de borda em que a versão comunitária RStudio executa. Quando criar um cluster do HDInsight, tem de fornecer dois utilizadores -- um utilizador HTTP e outro SSH:

![Parâmetros de login do portal HDI Azure](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Nome de utilizador do cluster**: um utilizador HTTP utilizado para autenticação através do gateway do HDInsight que é utilizado para proteger os clusters do HDInsight que criou. Este utilizador http é usado para aceder ao Apache Ambari UI, Apache Hadoop YARN UI, bem como outros componentes ui.
- **Nome de utilizador Secure Shell (SSH)**: um utilizador SSH para aceder ao cluster através de secure shell. Este utilizador é um utilizador no sistema Linux para todos os nós principais, nós de trabalho e nós de extremidade. Desta forma, pode utilizar o secure shell para aceder a qualquer nó num cluster remoto.

A versão R Studio Server Community utilizada no cluster ml Services no HDInsight aceita apenas o nome de utilizador e a palavra-passe do Linux como um mecanismo de entrada. Não suporta a transmissão de tokens. Por isso, quando tentar aceder ao R Studio pela primeira vez num cluster de Serviços ML, precisa de iniciar sessão duas vezes.

- Primeiro iniciar sessão utilizando as credenciais de utilizador HTTP através do HDInsight Gateway.

- Em seguida, utilize as credenciais de utilizador sSH para iniciar sessão no RStudio.
  
Atualmente, só é possível criar uma conta de utilizador SSH quando são aprovisionados clusters do HDInsight. Assim, para permitir que vários utilizadores acedam ao cluster ml Services no HDInsight, tem de criar utilizadores adicionais no sistema Linux.

Como o RStudio corre no nó de borda do cluster, há vários passos aqui:

1. Utilize o utilizador SSH existente para iniciar sessão no nó de borda
2. Adicionar mais utilizadores do Linux ao nó de extremidade
3. Utilizar a versão de comunidade do RStudio com o utilizador criado

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Passo 1: Utilize o utilizador SSH criado para iniciar sessão no nó da borda

Siga as instruções em [Connect to HDInsight (Apache Hadoop) utilizando o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) para aceder ao nó de borda. O endereço do nó de borda para `CLUSTERNAME-ed-ssh.azurehdinsight.net`o cluster ml services no HDInsight é .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Passo 2: Adicionar mais utilizadores do Linux ao nó de extremidade

Para adicionar um utilizador ao nó de extremidade, execute os comandos:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

A imagem que se segue mostra as saídas.

![screenshot saída utilizadores simultâneos](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Quando solicitado para "A palavra-passe Kerberos atual:", basta pressionar **Enter** para ignorá-la. A opção `-m` no comando `useradd` indica que o sistema vai criar uma pasta raiz para o utilizador, que é necessária na versão de comunidade do RStudio.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Passo 3: Utilizar a versão de comunidade do RStudio com o utilizador criado

Access RStudio `https://CLUSTERNAME.azurehdinsight.net/rstudio/`a partir de . Se estiver a fazer login pela primeira vez após a criação do cluster, introduza as credenciais de administrador do cluster seguidas pelas credenciais de utilizador SSH que criou. Se este não for o seu primeiro login, introduza apenas as credenciais para o utilizador SSH que criou.

Também pode iniciar sessão utilizando as credenciais originais (por predefinição, é *sshuser)* simultaneamente a partir de outra janela do navegador.

Repare também que os utilizadores adicionados recentemente não têm privilégios de raiz no sistema Linux, mas têm acesso a todos os ficheiros no armazenamento remoto do HDFS e do WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Ligue-se remotamente aos Serviços Microsoft ML

Pode configurar o acesso ao contexto de computação HDInsight Spark a partir de uma instância remota do Cliente ML que corre no seu ambiente de trabalho. Para isso, deve especificar as opções (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript) ao definir o contexto computacional RxSpark no seu ambiente de trabalho: Por exemplo:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Para mais informações, consulte a secção "Using Microsoft Machine Learning Server as a Apache Hadoop Client" em Como utilizar o [RevoScaleR num contexto](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) de computação Apache Spark

## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

O contexto de cálculo permite-lhe controlar se a computação é feita localmente no nó de extremidade ou distribuída pelos nós do cluster do HDInsight.  Para um exemplo de definir um contexto de computação com o RStudio Server, consulte Executar um script R num cluster de [Serviços ML em Azure HDInsight usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir o código R por vários nós

Com os Serviços ML no HDInsight, pode pegar no código R existente `rxExec`e executá-lo em vários nós do cluster utilizando . Esta função é útil para fazer varrimentos ou simulações de parâmetros. O código abaixo é um exemplo de como utilizar `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se ainda estiver a utilizar o contexto Spark, este comando devolve o valor `(Sys.info()["nodename"])` do nome do nó para os nós do trabalhador em que o código é executado. Por exemplo, num cluster de quatro nós, espera-se receber uma saída semelhante ao seguinte corte:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Dados de acesso na Apache Hive e Parquet

Os Serviços HDInsight ML permitem o acesso direto aos dados na Hive e Parquet para utilização por funções ScaleR no contexto da computação Spark. Estas capacidades estão disponíveis através de novas funções de origem de dados de ScaleR chamadas RxHiveData e RxParquetData, as quais funcionam com a utilização do Spark SQL para carregar dados diretamente para um DataFrame do Spark para análise por parte do ScaleR.

O código abaixo disponibiliza alguns códigos de exemplos de utilização das funções novas:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Para obter informações adicionais sobre a utilização destas novas `?RxHivedata` `?RxParquetData` funções consulte a ajuda online nos Serviços ML através da utilização dos comandos e comandos.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instale pacotes R adicionais no cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Para instalar pacotes R no nó de borda

Se pretender instalar pacotes R adicionais no nó `install.packages()` de borda, pode utilizar diretamente a partir da consola R, uma vez ligado ao nó de borda através do SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Para instalar pacotes R no nó do trabalhador

Para instalar pacotes R nos nós do trabalhador do cluster, deve utilizar uma Ação de Script. As Ações de Script são scripts de Bash utilizadas para fazer alterações de configuração ao cluster do HDInsight ou para instalar mais software, como pacotes de R adicionais. 

> [!IMPORTANT]  
> Só é possível utilizar as Ações de Script para instalar pacotes de R adicionais depois de o cluster ter sido criado. Não utilize este procedimento durante a criação do cluster, uma vez que o script depende de serviços ML estarem completamente configurados.

1. Siga os [passos](../hdinsight-hadoop-customize-cluster-linux.md)em Personalizar clusters utilizando a Ação do Script .

3. Para submeter a ação do **script,** forneça as seguintes informações:

   * Para **o tipo script,** selecione **Custom**.

   * Para **Nome**, forneça um nome para a ação do script.

     * Para **o guião de Bash URI,** insira `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Este é o script que instala pacotes R adicionais no nó do trabalhador

   * Selecione a caixa de verificação apenas para **O Trabalhador**.

   * **Parâmetros**: os pacotes de R que vão ser instalados. Por exemplo, `bitops stringr arules`

   * Selecione a caixa de verificação para **persistir esta ação de script**.  

   > [!NOTE]
   > 1. Por predefinição, todos os pacotes R são instalados a partir de uma imagem instantânea do repositório MRAN da Microsoft consistente com a versão do Ml Server que foi instalada. Se quiser instalar versões novas dos pacotes, existe algum risco de incompatibilidades. Contudo, este tipo de instalação é possível mediante a especificação de `useCRAN` como o primeiro elemento da lista do pacote, como, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes de R requerem bibliotecas do sistema Linux adicionais. Por conveniência, os Serviços HDInsight ML vêm pré-instalados com as dependências necessárias pelos 100 pacotes R mais populares. No entanto, se o pacote ou pacotes de R que instalar requeiram mais bibliotecas para além destas, tem de transferir o script de base utilizado aqui e adicionar passos para instalar as bibliotecas do sistema. Em seguida, tem de carregar o script modificado para um contentor de blobs público no armazenamento do Azure e utilizá-lo para instalar os pacotes.
   >    Para obter mais informações sobre como programar Ações de Script, veja [Script Action development (Programação de Ações de Script)](../hdinsight-hadoop-script-actions-linux.md).

   ![Portal Azure submete ação de script](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Selecione **Criar** para executar o script. Após a conclusão do script, os pacotes de R estão disponíveis em todos os nós de trabalho.

## <a name="next-steps"></a>Passos seguintes

* [Operacionalizar cluster dos Serviços ML no HDInsight](r-server-operationalize.md)
* [Opções de contexto computacional para cluster de serviço ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)
