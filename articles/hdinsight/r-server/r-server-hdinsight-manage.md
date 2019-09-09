---
title: Gerenciar o cluster de serviços am no HDInsight – Azure
description: Saiba como gerenciar várias tarefas no cluster de serviços de am no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 568c4324f3b542e2b913596c1d93ffb72bbbaec7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814241"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Gerenciar o cluster de serviços am no Azure HDInsight

Neste artigo, você aprende a gerenciar um cluster existente dos serviços ML no Azure HDInsight para executar tarefas como adicionar vários usuários simultâneos, conectar-se remotamente a um cluster de serviços de ML, alterar o contexto de computação, etc.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços do ML no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Serviços ml** para o **tipo de cluster**.


* Um cliente Secure Shell (SSH): Um cliente SSH é usado para se conectar remotamente ao cluster HDInsight e executar comandos diretamente no cluster. Para obter mais informações, consulte [usar SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="enable-multiple-concurrent-users"></a>Permitir vários utilizadores em simultâneo

Você pode habilitar vários usuários simultâneos para o cluster de serviços am no HDInsight adicionando mais usuários ao nó de borda no qual a versão da Comunidade do RStudio é executada. Quando criar um cluster do HDInsight, tem de fornecer dois utilizadores -- um utilizador HTTP e outro SSH:

![Utilizador simultâneo 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nome de utilizador do cluster**: um utilizador HTTP utilizado para autenticação através do gateway do HDInsight que é utilizado para proteger os clusters do HDInsight que criou. Esse usuário HTTP é usado para acessar a interface do usuário do Apache Ambari, Apache Hadoop interface do usuário do YARN, bem como outros componentes da interface do usuário.
- **Nome de utilizador Secure Shell (SSH)** : um utilizador SSH para aceder ao cluster através de secure shell. Este utilizador é um utilizador no sistema Linux para todos os nós principais, nós de trabalho e nós de extremidade. Desta forma, pode utilizar o secure shell para aceder a qualquer nó num cluster remoto.

A versão da Comunidade do servidor R Studio usada no cluster de serviços de ML no HDInsight aceita apenas o nome de usuário e a senha do Linux como um mecanismo de entrada. Não suporta a transmissão de tokens. Portanto, ao tentar acessar o R Studio pela primeira vez em um cluster de serviços ML, você precisará entrar duas vezes.

- Primeiro, entre usando as credenciais de usuário HTTP por meio do gateway do HDInsight. 

- Em seguida, use as credenciais de usuário do SSH para entrar no RStudio.
  
Atualmente, só é possível criar uma conta de utilizador SSH quando são aprovisionados clusters do HDInsight. Portanto, para permitir que vários usuários acessem o cluster de serviços ML no HDInsight, você deve criar usuários adicionais no sistema Linux.

Como o RStudio é executado no nó de borda do cluster, há várias etapas aqui:

1. Usar o usuário SSH existente para entrar no nó de borda
2. Adicionar mais utilizadores do Linux ao nó de extremidade
3. Utilizar a versão de comunidade do RStudio com o utilizador criado

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Passo 1: Usar o usuário SSH criado para entrar no nó de borda

Siga as instruções em [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para acessar o nó de borda. O endereço do nó de borda para o cluster de serviços `CLUSTERNAME-ed-ssh.azurehdinsight.net`am no HDInsight é.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Passo 2: Adicionar mais utilizadores do Linux ao nó de extremidade

Para adicionar um utilizador ao nó de extremidade, execute os comandos:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

A captura de tela a seguir mostra as saídas.

![Utilizador simultâneo 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Quando for solicitada a "senha atual do Kerberos:", basta pressionar **Enter** para ignorá-la. A opção `-m` no comando `useradd` indica que o sistema vai criar uma pasta raiz para o utilizador, que é necessária na versão de comunidade do RStudio.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Passo 3: Utilizar a versão de comunidade do RStudio com o utilizador criado

Acesse RStudio https://CLUSTERNAME.azurehdinsight.net/rstudio/ de. Se você estiver fazendo logon pela primeira vez depois de criar o cluster, insira as credenciais de administrador do cluster seguidas pelas credenciais de usuário do SSH que você criou. Se este não for seu primeiro logon, insira apenas as credenciais para o usuário SSH que você criou.

Você também pode entrar usando as credenciais originais (por padrão, é *sshuser*) simultaneamente de outra janela do navegador.

Repare também que os utilizadores adicionados recentemente não têm privilégios de raiz no sistema Linux, mas têm acesso a todos os ficheiros no armazenamento remoto do HDFS e do WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Conectar-se remotamente aos serviços do Microsoft ML

Você pode configurar o acesso ao contexto de computação do HDInsight Spark de uma instância remota do cliente do ML em execução na sua área de trabalho. Para fazer isso, você deve especificar as opções (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript) ao definir o contexto de computação RxSpark em sua área de trabalho: Por exemplo:

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

Para obter mais informações, consulte a seção "usando Microsoft Machine Learning Server como um cliente do Apache Hadoop" em [como usar o RevoScaleR em um contexto de computação do Apache Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

O contexto de cálculo permite-lhe controlar se a computação é feita localmente no nó de extremidade ou distribuída pelos nós do cluster do HDInsight.  Para obter um exemplo de como definir um contexto de computação com o RStudio Server, consulte [executar um script R em um cluster de serviços do ml no Azure HDInsight usando o servidor RStudio](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir o código R por vários nós

Com os serviços do ML no HDInsight, você pode usar o código R existente e executá-lo em vários nós no `rxExec`cluster usando o. Esta função é útil para fazer varrimentos ou simulações de parâmetros. O código abaixo é um exemplo de como utilizar `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se você ainda estiver usando o contexto do Spark, esse comando retornará o valor de NodeName para os nós de `(Sys.info()["nodename"])` trabalho em que o código é executado. Por exemplo, em um cluster de quatro nós, você espera receber uma saída semelhante ao trecho a seguir:

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

## <a name="access-data-in-apache-hive-and-parquet"></a>Acessar dados em Apache Hive e parquet

Os serviços do HDInsight ML permitem o acesso direto aos dados no hive e no parquet para uso por funções scaler no contexto de computação do Spark. Estas capacidades estão disponíveis através de novas funções de origem de dados de ScaleR chamadas RxHiveData e RxParquetData, as quais funcionam com a utilização do Spark SQL para carregar dados diretamente para um DataFrame do Spark para análise por parte do ScaleR.

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


Para obter informações adicionais sobre o `?RxHivedata` uso dessas novas funções, consulte a ajuda online nos serviços do ml por meio do uso dos comandos e. `?RxParquetData`  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalar pacotes R adicionais no cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Para instalar pacotes do R no nó de borda

Se você quiser instalar pacotes R adicionais no nó de borda, poderá usar `install.packages()` diretamente de dentro do console do r, uma vez conectado ao nó de borda por meio de SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Para instalar pacotes do R no nó de trabalho

Para instalar pacotes do R nos nós de trabalho do cluster, você deve usar uma ação de script. As Ações de Script são scripts de Bash utilizadas para fazer alterações de configuração ao cluster do HDInsight ou para instalar mais software, como pacotes de R adicionais. 

> [!IMPORTANT]  
> Só é possível utilizar as Ações de Script para instalar pacotes de R adicionais depois de o cluster ter sido criado. Não use esse procedimento durante a criação do cluster, pois o script depende de os serviços ML estarem completamente configurados.

1. Siga as etapas em [Personalizar clusters usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. Para **enviar a ação de script**, forneça as seguintes informações:

   * Para **tipo de script**, selecione **personalizado**.

   * Para **nome**, forneça um nome para a ação de script.

     * Para **URI de script bash**, `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`insira. Este é o script que instala pacotes R adicionais no nó de trabalho

   * Marque a caixa de seleção somente para o **trabalho**.

   * **Parâmetros**: Os pacotes de R a serem instalados. Por exemplo, `bitops stringr arules`

   * Marque a caixa de seleção para **persistir essa ação de script**.  

   > [!NOTE]
   > 1. Por padrão, todos os pacotes do R são instalados de um instantâneo do repositório do Microsoft MRAN consistente com a versão do ML Server que foi instalado. Se quiser instalar versões novas dos pacotes, existe algum risco de incompatibilidades. Contudo, este tipo de instalação é possível mediante a especificação de `useCRAN` como o primeiro elemento da lista do pacote, como, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes de R requerem bibliotecas do sistema Linux adicionais. Para sua conveniência, os serviços de ML do HDInsight vêm pré-instalados com as dependências necessárias pelos principais pacotes de R 100 mais populares. No entanto, se o pacote ou pacotes de R que instalar requeiram mais bibliotecas para além destas, tem de transferir o script de base utilizado aqui e adicionar passos para instalar as bibliotecas do sistema. Em seguida, tem de carregar o script modificado para um contentor de blobs público no armazenamento do Azure e utilizá-lo para instalar os pacotes.
   >    Para obter mais informações sobre como programar Ações de Script, veja [Script Action development (Programação de Ações de Script)](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Adicionar uma ação de script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Selecione **Criar** para executar o script. Após a conclusão do script, os pacotes de R estão disponíveis em todos os nós de trabalho.

## <a name="next-steps"></a>Passos Seguintes

* [Operacionalizar cluster dos Serviços ML no HDInsight](r-server-operationalize.md)
* [Opções de contexto de computação para o cluster de serviço do ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)
