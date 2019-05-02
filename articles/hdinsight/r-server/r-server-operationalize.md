---
title: Operacionalizar os serviços de ML no HDInsight - Azure
description: Saiba como operacionalizar os serviços de ML no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 916c4fae8eed9451080f92e97743876e89bd25ea
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719737"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacionalizar o cluster de serviços de ML no Azure HDInsight

Depois de ter usado o cluster de serviços de ML no HDInsight para concluir a sua modelação de dados, pode operacionalizar o modelo para fazer previsões. Este artigo fornece instruções sobre como efetuar esta tarefa.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster de serviços de ML no HDInsight**: Para obter instruções, consulte [introdução aos serviços de ML no HDInsight](r-server-get-started.md).

* **Um cliente Secure Shell (SSH)**: Um cliente SSH é utilizado para ligar ao HDInsight cluster e executar comandos diretamente no cluster remotamente. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacionalizar o cluster de serviços de ML com configuração one box

> [!NOTE]  
> Os passos abaixo se aplicam ao R Server 9.0 e ML Server 9.1. Para 9.3 do ML Server, consulte [utilizar a ferramenta de administração para gerir a configuração da operacionalização](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Aceda através de SSH ao nó de extremidade.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Para obter instruções sobre como utilizar o SSH com o Azure HDInsight, consulte [utilizar o SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Altere o diretório para a versão relevante e utilize sudo do dll de rede ponto: 

    - Para o Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Para o Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. É apresentada com as opções à sua escolha. Escolha a primeira opção, conforme mostrado na captura de ecrã seguinte, a **configurar o ML Server para Operacionalização**.

    ![operacionalização de one-box](./media/r-server-operationalize/admin-util-one-box-1.png)

1. É apresentado com a opção de escolher como pretende operacionalizar ML Server. Entre as opções apresentadas, escolha o método primeiro introduzindo **A**.

    ![operacionalização de one-box](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Quando lhe for pedido, introduza e reintroduza a palavra-passe para um utilizador de administrador local.

1. Deverá ver saídas sugerindo que a operação foi concluída com êxito. Também lhe for pedido para selecionar outra opção no menu. Selecione E para regressar ao menu principal.

    ![operacionalização de one-box](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Opcionalmente, pode executar verificações de diagnóstico ao executar um teste de diagnóstico da seguinte forma:

    a. A partir do menu principal, selecione **6** para executar testes de diagnóstico.

    ![operacionalização de one-box](./media/r-server-operationalize/diagnostic-1.png)

    b. No menu de testes de diagnóstico, selecione **A**. Quando lhe for pedido, introduza a palavra-passe que indicou para o utilizador de administrador local.

    ![operacionalização de one-box](./media/r-server-operationalize/diagnostic-2.png)

    c. Certifique-se de que o resultado mostra que o estado de funcionamento geral é uma passagem.

    ![operacionalização de one-box](./media/r-server-operationalize/diagnostic-3.png)

    d. Entre as opções de menu apresentadas, introduza **i** para regressar ao menu principal e, em seguida, introduza **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Grandes atrasos ao consumir o serviço web no Apache Spark

Se ocorrerem atrasos longos quando tentar consumir um serviço web criado com o contexto de cálculo de funções mrsdeploy do Apache Spark, poderá ter de adicionar algumas pastas em falta. A aplicação Spark pertence a um utilizador chamado "*rserve2*" sempre que é invocado a partir de um serviço Web utilizando funções mrsdeploy. Para contornar este problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Nesta fase, a configuração da operacionalização está concluída. Agora, pode utilizar o `mrsdeploy` pacote no seu RClient para se ligar à operacionalização no nó de extremidade e começar a utilizar os seus recursos, como [execução remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Consoante o seu cluster estar ou não configurado numa rede virtual, poderá ter de configurar o túnel de encaminhamento de portas através do início de sessão SSH. As secções a seguir explicam como configurar este túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster de ML serviços na rede virtual

Confirme que permite o tráfego pela porta 12800 para o nó de extremidade. Desta forma, pode utilizar o nó de extremidade para ligar à funcionalidade Operacionalização.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` não se conseguir ligar ao nó de extremidade, mas conseguir aceder a este através de SSH, tem de verificar se a regra para permitir o tráfego na porta 12800 foi devidamente configurada ou não. Se o problema persistir, pode utilizar uma solução, que consiste em configurar o túnel de encaminhamento de portas através de SSH. Para obter instruções, consulte a secção seguinte:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Não configurado numa rede virtual de cluster de serviços de ML

Se o seu cluster não estiver configurado numa VNet ou se estiver com problemas de conectividade através da VNet, pode utilizar o túnel de encaminhamento de portas SSH.

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Quando a sessão SSH estiver ativa, o tráfego da porta 12800 do computador local é encaminhado para porta o nó de extremidade 12800 através dessa sessão. Confirme que utiliza `127.0.0.1:12800` no método `remoteLogin()`. Isso inicia sessão na operacionalização do nó de extremidade através do encaminhamento de porta.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Nós em nós de trabalho do HDInsight de computação de dimensionamento operacionalizado

Para dimensionar os nós de computação, primeiro desativar os nós de trabalho e, em seguida, configurar nós de computação em nós de trabalho desativado.

### <a name="step-1-decommission-the-worker-nodes"></a>Passo 1: Descontinuar os nós de trabalho

Cluster de serviços de ML não é gerido através de [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Se os nós de trabalho não estiverem desativados, o YARN Resource Manager não funciona conforme esperado, porque não tem conhecimento dos recursos de, que está a ser tomados pelo servidor. Para evitar esta situação, recomendamos a desativação dos nós de trabalho antes de aumentar horizontalmente os nós de computação.

Siga estes passos para desativar nós de trabalho:

1. Inicie sessão na consola do Ambari do cluster e clique em **anfitriões** separador.

1. Selecione nós de trabalho (para ser desativado).

1. Clique em **ações** > **Selected Hosts** > **anfitriões** > **ativar o modo de manutenção**. Por exemplo, na imagem seguinte, selecionámos a desativação de wn3 e wn4.  

   ![desativar os nós de trabalho](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecione **ações** > **anfitriões selecionados** > **DataNodes** > clique em **decommission (descontinuar)**.
* Selecione **ações** > **anfitriões selecionados** > **NodeManagers** > clique em **decommission (descontinuar)**.
* Selecione **ações** > **anfitriões selecionados** > **DataNodes** > clique em **parar**.
* Selecione **ações** > **anfitriões selecionados** > **NodeManagers** > clique em **parar**.
* Selecione **ações** > **anfitriões selecionados** > **anfitriões** > clique em **parar todos os componentes**.
* Desmarque os nós de trabalho e selecione os nós principais.
* Selecione **ações** > **Selected Hosts** > "**anfitriões** > **reiniciar todos os componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Passo 2: Configurar os nós de computação em cada nó de trabalho desativado

1. Aceda através de SSH a cada nó de trabalho desativado.

1. Execute o utilitário de administração com o DLL relevante para o cluster de serviços de ML que tiver. Para ML Server 9.1, execute o seguinte:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Introduza **1** para selecionar a opção **configurar ML Server para Operacionalização**.

1. Introduza **C** para selecionar a opção `C. Compute node`. Desta forma, o nó de computação é configurado no nó de trabalho.

1. Saia do Utilitário de Administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Passo 3: Adicionar detalhes de nós de computação no nó da web

Depois de todos os nós de trabalho desativados estiverem configurados para executar o nó de computação, regresse ao nó de extremidade e adicione endereços IP de nós de trabalho desativado na configuração do nó do servidor de ML web:

1. Aceda através de SSH ao nó de extremidade.

1. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Procure a secção "Uris" e adicionar IP do nó de trabalho e os detalhes da porta.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Passos Seguintes

* [Gerir cluster dos Serviços ML no HDInsight](r-server-hdinsight-manage.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)
