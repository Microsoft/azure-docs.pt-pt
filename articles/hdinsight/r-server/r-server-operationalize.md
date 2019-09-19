---
title: Operacionalize os serviços de ML no HDInsight – Azure
description: Saiba como colocar seu modelo de dados em operação para fazer previsões com os serviços de ML no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123570"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacionalize o cluster de serviços ML no Azure HDInsight

Depois de usar o cluster de serviços do ML no HDInsight para concluir a modelagem de dados, você pode colocar o modelo em operação para fazer previsões. Este artigo fornece instruções sobre como executar essa tarefa.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços do ML no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Serviços ml** para o **tipo de cluster**.

* Um cliente Secure Shell (SSH): Um cliente SSH é usado para se conectar remotamente ao cluster HDInsight e executar comandos diretamente no cluster. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Colocar em operação o cluster de serviços ML com a configuração de uma caixa

> [!NOTE]  
> As etapas a seguir são aplicáveis ao R Server 9,0 e ML Server 9,1. Para ML Server 9,3, consulte [usar a ferramenta de administração para gerenciar a configuração de operacionalização](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Aceda através de SSH ao nó de extremidade.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Para obter instruções sobre como usar SSH com o Azure HDInsight, consulte [usar SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Altere o diretório para a versão relevante e sudo a DLL do dot net: 

    - Para o Microsoft ML Server 9,1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Para o Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Você verá as opções para escolher. Escolha a primeira opção, conforme mostrado na captura de tela a seguir, para **configurar ml Server para operacionalização**.

    ![Seleção do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Agora, você verá a opção de escolher como deseja colocar em operação ML Server. Nas opções apresentadas, escolha a primeira inserindo **uma**.

    ![Operacionalização do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Quando solicitado, insira e insira novamente a senha para um usuário administrador local.

1. Você deve ver as saídas sugerindo que a operação foi bem-sucedida. Você também será solicitado a selecionar outra opção no menu. Selecione E para voltar ao menu principal.

    ![Êxito do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Opcionalmente, você pode executar verificações de diagnóstico executando um teste de diagnóstico da seguinte maneira:

    a. No menu principal, selecione **6** para executar testes de diagnóstico.

    ![Diagnóstico do utilitário de administração do R Server](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. No menu testes de diagnóstico, selecione **um**. Quando solicitado, insira a senha que você forneceu para o usuário administrador local.

    ![Teste do utilitário de administração do servidor R](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Verifique se a saída mostra que a integridade geral é uma passagem.

    ![Passagem do utilitário de administração do R Server](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Nas opções de menu apresentadas, digite **e** para retornar ao menu principal e, em seguida, digite **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Atrasos longos ao consumir o serviço Web no Apache Spark

Se você encontrar longos atrasos ao tentar consumir um serviço Web criado com funções mrsdeploy em um contexto de computação Apache Spark, talvez seja necessário adicionar algumas pastas ausentes. A aplicação Spark pertence a um utilizador chamado "*rserve2*" sempre que é invocado a partir de um serviço Web utilizando funções mrsdeploy. Para contornar este problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Nesta fase, a configuração da operacionalização está concluída. Agora você pode usar o `mrsdeploy` pacote em seu RClient para se conectar à operacionalização no nó de borda e começar a usar seus recursos, como [execução remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e [Serviços Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Consoante o seu cluster estar ou não configurado numa rede virtual, poderá ter de configurar o túnel de encaminhamento de portas através do início de sessão SSH. As secções a seguir explicam como configurar este túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster de serviços de ML na rede virtual

Confirme que permite o tráfego pela porta 12800 para o nó de extremidade. Desta forma, pode utilizar o nó de extremidade para ligar à funcionalidade Operacionalização.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` não se conseguir ligar ao nó de extremidade, mas conseguir aceder a este através de SSH, tem de verificar se a regra para permitir o tráfego na porta 12800 foi devidamente configurada ou não. Se o problema persistir, pode utilizar uma solução, que consiste em configurar o túnel de encaminhamento de portas através de SSH. Para obter instruções, consulte a seguinte seção:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster de serviços de ML não configurado na rede virtual

Se o seu cluster não estiver configurado numa VNet ou se estiver com problemas de conectividade através da VNet, pode utilizar o túnel de encaminhamento de portas SSH.

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Quando a sessão SSH estiver ativa, o tráfego da porta 12800 do computador local será encaminhado para a porta 12800 do nó de borda por meio da sessão SSH. Confirme que utiliza `127.0.0.1:12800` no método `remoteLogin()`. Isso faz logon na operacionalização do nó de borda por meio do encaminhamento de porta.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Dimensionar nós de computação operacional em nós de trabalho do HDInsight

Para dimensionar os nós de computação, primeiro Encerre os nós de trabalho e, em seguida, configure os nós de computação nos nós de trabalho descomissionados.

### <a name="step-1-decommission-the-worker-nodes"></a>Passo 1: Descontinuar os nós de trabalho

O cluster de serviços de ML não é gerenciado por meio de [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Se os nós de trabalho não forem encerrados, o Gerenciador de recursos YARN não funcionará conforme o esperado porque não está ciente dos recursos que estão sendo usados pelo servidor. Para evitar esta situação, recomendamos a desativação dos nós de trabalho antes de aumentar horizontalmente os nós de computação.

Siga estas etapas para encerrar os nós de trabalho:

1. Faça logon no console do Ambari do cluster e clique na guia **hosts** .

1. Selecione os nós de trabalho (a serem descomissionados).

1. Clique em **ações** > **hosts** > selecionados**hosts** > **ativar o modo de manutenção**. Por exemplo, na imagem seguinte, selecionámos a desativação de wn3 e wn4.  

   ![O Apache Ambari ativa o modo de manutenção](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecione **ações** > **hosts** > selecionados**datanodes** > clique em desativar.
* Selecione **ações** >  **hosts** > selecionados NodeManagers > clique em **desativar**.
* Selecione **ações** >  **hosts** > selecionados nós de > clique em **parar**.
* Selecione **ações** >  **hosts** > selecionados NodeManagers > clique em **parar**.
* Selecione **ações** > **hosts** > selecionados**hosts** > clique em **parar todos os componentes**.
* Desmarque os nós de trabalho e selecione os nós principais.
* Selecione **ações** > **hosts selecionados** > "**hosts** > **reiniciar todos os componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Passo 2: Configurar nós de computação em cada nó (s) de trabalho descomissionado

1. Aceda através de SSH a cada nó de trabalho desativado.

1. Execute o utilitário de administração usando a DLL relevante para o cluster de serviços ML que você tem. Para ML Server 9,1, execute o seguinte:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Digite **1** para selecionar a opção **Configurar ml Server para operacionalização**.

1. Digite **C** para selecionar a `C. Compute node`opção. Desta forma, o nó de computação é configurado no nó de trabalho.

1. Saia do Utilitário de Administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Passo 3: Adicionar detalhes de nós de computação no nó da Web

Depois que todos os nós de trabalho descomissionados estiverem configurados para executar o nó de computação, volte ao nó de borda e adicione os endereços IP dos nós de trabalho descomissionados na configuração do nó do ML Server Web:

1. Aceda através de SSH ao nó de extremidade.

1. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Procure a seção "URIs" e adicione os detalhes de IP e de porta do nó de trabalho.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Passos seguintes

* [Gerir cluster dos Serviços ML no HDInsight](r-server-hdinsight-manage.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)
