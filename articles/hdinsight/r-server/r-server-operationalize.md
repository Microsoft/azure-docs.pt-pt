---
title: Operacionalizar os Serviços ML no HDInsight - Azure
description: Saiba como operacionalizar o seu modelo de dados para fazer previsões com os Serviços ML em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71123570"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacionalizar o cluster de serviços ml no Azure HDInsight

Depois de ter usado o cluster ML Services no HDInsight para completar a sua modelação de dados, pode operacionalizar o modelo para fazer previsões. Este artigo fornece instruções sobre como executar esta tarefa.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços ML no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **ml Services** for **Cluster type**.

* Um cliente Secure Shell (SSH): é utilizado um cliente SSH para ligar remotamente ao cluster do HDInsight e executar comandos diretamente no mesmo. Para mais informações, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacionalizar cluster de serviços ML com configuração de uma caixa

> [!NOTE]  
> Os passos abaixo são aplicáveis ao R Server 9.0 e ao Servidor ML 9.1. Para o Servidor ML 9.3, consulte [utilize a ferramenta de administração para gerir a configuração de operacionalização](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Aceda através de SSH ao nó de extremidade.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Para obter instruções sobre como utilizar o SSH com o Azure HDInsight, consulte [Use SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Alterar o diretório para a versão relevante e sudo o dll do t net dll: 

    - Para o Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Para o Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. É-lhe apresentadas as opções para escolher. Escolha a primeira opção, como mostra a seguinte imagem, para configurar o **Servidor ML para operacionalização**.

    ![Selecione de utilitário de administração de servidorR](./media/r-server-operationalize/admin-util-one-box-1.png)

1. É-lhe agora apresentada a opção de escolher como pretende operacionalizar o Servidor ML. Entre as opções apresentadas, escolha a primeira entrando em **A**.

    ![Utilitário de administração de servidorR operacionalizar](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Quando solicitado, introduza e reintroduza a palavra-passe para um utilizador administrativo local.

1. Devia ver saídas que sugerem que a operação foi bem sucedida. Também é solicitado a selecionar outra opção do menu. Selecione E para voltar ao menu principal.

    ![Sucesso de utilidade de administração de servidorr R](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Opcionalmente, pode efetuar verificações de diagnóstico executando um teste de diagnóstico da seguinte forma:

    a. A partir do menu principal, selecione **6** para eexecutar testes de diagnóstico.

    ![Diagnóstico de utilidade de administração de servidorR](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. No menu de Testes de Diagnóstico, selecione **A**. Quando solicitado, introduza a palavra-passe que forneceu ao utilizador administrativo local.

    ![Teste de utilidade da administração do servidor R](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Verifique se a saída mostra que a saúde geral é um passe.

    ![Passe de utilidade de administração de servidorR](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. A partir das opções de menu apresentadas, insira **E** para voltar ao menu principal e, em seguida, insira **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Longos atrasos ao consumir serviço web em Apache Spark

Se encontrar longos atrasos ao tentar consumir um serviço web criado com funções de mrsdeploy num contexto de computação Apache Spark, poderá ter de adicionar algumas pastas em falta. A aplicação Spark pertence a um utilizador chamado "*rserve2*" sempre que é invocado a partir de um serviço Web utilizando funções mrsdeploy. Para contornar este problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Nesta fase, a configuração da operacionalização está concluída. Agora pode utilizar `mrsdeploy` o pacote no seu RClient para se ligar à operacionalização no nó de borda e começar a usar as suas funcionalidades como [execução remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) e [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Consoante o seu cluster estar ou não configurado numa rede virtual, poderá ter de configurar o túnel de encaminhamento de portas através do início de sessão SSH. As secções a seguir explicam como configurar este túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster de serviços ML na rede virtual

Confirme que permite o tráfego pela porta 12800 para o nó de extremidade. Desta forma, pode utilizar o nó de extremidade para ligar à funcionalidade Operacionalização.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` não se conseguir ligar ao nó de extremidade, mas conseguir aceder a este através de SSH, tem de verificar se a regra para permitir o tráfego na porta 12800 foi devidamente configurada ou não. Se o problema persistir, pode utilizar uma solução, que consiste em configurar o túnel de encaminhamento de portas através de SSH. Para obter instruções, consulte a seguinte secção:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster de serviços ML não configurado em rede virtual

Se o seu cluster não estiver configurado numa VNet ou se estiver com problemas de conectividade através da VNet, pode utilizar o túnel de encaminhamento de portas SSH.

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Uma vez que a sua sessão sSH esteja ativa, o tráfego do porto 12800 da sua máquina local é encaminhado para o porto do nó 12800 através da sessão SSH. Confirme que utiliza `127.0.0.1:12800` no método `remoteLogin()`. Isto entra na operacionalização do nó de borda através do encaminhamento do porto.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Nósdeos de cálculo operacionalizados em nódos de trabalhadores da HDInsight

Para escalar os nós de computação, primeiro desativa os nós dos trabalhadores e, em seguida, configurar nós de computação nos nós dos trabalhadores desativados.

### <a name="step-1-decommission-the-worker-nodes"></a>Passo 1: Desativar os nódosos dos trabalhadores

O cluster ml Services não é gerido através do [YARN Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Se os nós dos trabalhadores não forem desativados, o Gestor de Recursos da ARN não funciona como esperado porque não tem conhecimento dos recursos que estão a ser recolhidos pelo servidor. Para evitar esta situação, recomendamos a desativação dos nós de trabalho antes de aumentar horizontalmente os nós de computação.

Siga estes passos para desativar os nódosos dos trabalhadores:

1. Inicie sessão na consola Ambari do cluster e clique no separador **Hosts.**

1. Selecione os nódosos dos trabalhadores (a desativar).

1. Clique **em ações** > **Os anfitriões selecionados** > **Hosts** > **liguem o modo de manutenção**. Por exemplo, na imagem seguinte, selecionámos a desativação de wn3 e wn4.  

   ![Apache Ambari ligar modo de manutenção](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecione **ações** > **selecionadas Os** > **DataNódes** > clicar em **Desativação**.
* **Selecione ações Selecionadas** > **Anfitriões** > **NodeManagers** > clique em **Desativação**.
* **Selecione ações** > **selecionadas Os** > **DataNódes** > clicar em **Parar**.
* **Selecione ações Selecionadas** > **Os Anfitriões** > **NodeManagers** Selecionados > clique em **Stop**.
* **Selecione ações** > **anfitriões selecionados** > **Hosts** > clique em parar todos **os componentes**.
* Desmarque os nós de trabalho e selecione os nós principais.
* Selecione **ações selecionadas** > **anfitriões selecionados** > "**Anfitriões** > reiniciar todos os**componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Passo 2: Configure os nós de cálculo em cada nó de trabalhador desativado

1. Aceda através de SSH a cada nó de trabalho desativado.

1. Executar utilitário de administração utilizando o DLL relevante para o cluster ml Services que você tem. Para o Servidor ML 9.1, executar o seguinte:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Introduza **1** para selecionar opção **Configure ML Server para operacionalização**.

1. Introduza **C** `C. Compute node`para selecionar a opção . Desta forma, o nó de computação é configurado no nó de trabalho.

1. Saia do Utilitário de Administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Passo 3: Adicionar detalhes dos nódosos computacionais no nó web

Uma vez que todos os nós de trabalhador desativados estejam configurados para executar o nó de cálculo, volte ao nó da borda e adicione os endereços IP dos nós dos trabalhadores desativados na configuração do nó web do Servidor ML:

1. Aceda através de SSH ao nó de extremidade.

1. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Procure a secção "Uris" e adicione os detalhes ip e porta do nó do trabalhador.

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
