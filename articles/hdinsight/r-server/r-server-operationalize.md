---
title: Operacionalizar serviços ML em HDInsight - Azure
description: Saiba como operacionalizar o seu modelo de dados para fazer previsões com os Serviços ML em Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 7d597c4dbb81562050e9523c61d47d0020fc9059
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869480"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacionalizar o cluster de Serviços ML em Azure HDInsight

Depois de ter usado o cluster ML Services em HDInsight para completar a modelação de dados, pode operacionalizar o modelo para fazer previsões. Este artigo fornece instruções sobre como executar esta tarefa.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster ML Services em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **serviços ML** para **o tipo cluster**.

* Um cliente Secure Shell (SSH): é utilizado um cliente SSH para ligar remotamente ao cluster do HDInsight e executar comandos diretamente no mesmo. Para obter mais informações, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacionalizar o cluster de Serviços ML com configuração de uma caixa

> [!NOTE]  
> Os passos abaixo são aplicáveis ao R Server 9.0 e ao ML Server 9.1. Para o ML Server 9.3, consulte [a ferramenta de administração para gerir a configuração de operacionalização](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Aceda através de SSH ao nó de extremidade.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Para obter instruções sobre como utilizar o SSH com Azure HDInsight, consulte [Use SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Alterar o diretório para a versão relevante e sudo o dll net dot: 

    - Para o Microsoft ML Server 9.1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Para o Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. É-lhe apresentadas as opções à escolha. Escolha a primeira opção, como mostra a seguinte imagem, para Configurar o **Servidor ML para operacionalização**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="Seleção de utilitário de administração de servidor R" border="true":::

1. É agora apresentada a opção de escolher como pretende operacionalizar o ML Server. A partir das opções apresentadas, escolha a primeira entrando em **A**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="Operacionalização do utilitário de administração de servidor R" border="true":::

1. Quando solicitado, insira e reentre na palavra-passe para um utilizador administrativo local.

1. Devias ver saídas a sugerir que a operação foi um sucesso. Também é solicitado que selecione outra opção no menu. Selecione E para voltar ao menu principal.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="Sucesso de utilidade da administração do servidor R" border="true":::

1. Opcionalmente, pode efetuar verificações de diagnóstico executando um teste de diagnóstico da seguinte forma:

    a. A partir do menu principal, selecione **6** para realizar testes de diagnóstico.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="Diagnóstico de utilidade da administração do servidor R" border="true":::

    b. No menu Testes de Diagnóstico, selecione **A**. Quando solicitado, insira a palavra-passe que forneceu para o utilizador administrativo local.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="Teste de utilidade da administração do servidor R" border="true":::

    c. Verifique se a saída mostra que a saúde geral é um passe.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="Passe de utilidade da administração do servidor R" border="true":::

    d. A partir das opções de menu apresentadas, insira **E** para voltar ao menu principal e, em seguida, insira **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Longos atrasos ao consumir serviço web em Apache Spark

Se encontrar longos atrasos ao tentar consumir um serviço web criado com funções de mrsdeploy num contexto de computação Apache Spark, poderá ter de adicionar algumas pastas em falta. A aplicação Spark pertence a um utilizador chamado "*rserve2*" sempre que é invocado a partir de um serviço Web utilizando funções mrsdeploy. Para contornar este problema:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

Nesta fase, a configuração da operacionalização está concluída. Agora pode utilizar o `mrsdeploy` pacote no seu RClient para ligar à operacionalização no nó de borda e começar a usar as suas funcionalidades como [execução remota](/machine-learning-server/r/how-to-execute-code-remotely) e [serviços web](/machine-learning-server/operationalize/concept-what-are-web-services). Consoante o seu cluster estar ou não configurado numa rede virtual, poderá ter de configurar o túnel de encaminhamento de portas através do início de sessão SSH. As secções a seguir explicam como configurar este túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster de serviços ML na rede virtual

Confirme que permite o tráfego pela porta 12800 para o nó de extremidade. Desta forma, pode utilizar o nó de extremidade para ligar à funcionalidade Operacionalização.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Se `remoteLogin()` não se conseguir ligar ao nó de extremidade, mas conseguir aceder a este através de SSH, tem de verificar se a regra para permitir o tráfego na porta 12800 foi devidamente configurada ou não. Se o problema persistir, pode utilizar uma solução, que consiste em configurar o túnel de encaminhamento de portas através de SSH. Para obter instruções, consulte a seguinte secção:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster ML Services não montado em rede virtual

Se o seu cluster não estiver configurado numa VNet ou se estiver com problemas de conectividade através da VNet, pode utilizar o túnel de encaminhamento de portas SSH.

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Uma vez que a sua sessão de SSH esteja ativa, o tráfego da porta 12800 da sua máquina local é encaminhado para a porta 12800 do nó de borda através da sessão SSH. Confirme que utiliza `127.0.0.1:12800` no método `remoteLogin()`. Isto entra na operacionalização do nó de borda através do encaminhamento da porta.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Balança operacionalizada nódoas nos nos acenos dos trabalhadores hdInsight

Para escalar os nós computamento, primeiro desativa-se os nós dos trabalhadores e depois configura os nós computacional nos nós desativados do trabalhador.

### <a name="step-1-decommission-the-worker-nodes"></a>Passo 1: Desativar os nosdes dos trabalhadores

O cluster ML Services não é gerido através do [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Se os nós dos trabalhadores não forem desativados, o Gestor de Recursos YARN não funciona como esperado porque não tem conhecimento dos recursos que estão a ser recolhidos pelo servidor. Para evitar esta situação, recomendamos a desativação dos nós de trabalho antes de aumentar horizontalmente os nós de computação.

Siga estes passos para desativar os nóns dos trabalhadores:

1. Inicie sessão na consola Ambari do cluster e clique no separador **Anfitriões.**

1. Selecione os nóns dos trabalhadores (a ser desativado).

1. Clique **em Ações**  >  **Anfitriões Selecionados**  >  **Anfitriões Ligue** o modo de manutenção  >  **.** Por exemplo, na imagem seguinte, selecionámos a desativação de wn3 e wn4.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Apache Ambari liga o modo de manutenção" border="true":::  

* Selecione **ações**  >  **Selecionados Anfitriões**  >  **DataNodes** > clique em **Desativação**.
* Selecione **Ações**  >  **Selecionados**  >  **NodeManagers** > clique em **Desativação**.
* Selecione **ações**  >  **Selecionados**  >  **DataNodes** > clique em **Stop**.
* Selecione **Ações**  >  **Selecionados**  >  **NodeManagers** > clique em **Stop**.
* Selecione **Ações**  >  **Anfitriões**  >  **Selecionados Anfitriões** > clique **em Parar todos os componentes**.
* Desmarque os nós de trabalho e selecione os nós principais.
* Selecione **ações**  >  **Hostes selecionados** > "**Hosts**  >  **Restart All Components**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Passo 2: Configurar os nós computacional em cada nó ou nó de trabalhador desativado

1. Aceda através de SSH a cada nó de trabalho desativado.

1. Executar o utilitário de administração utilizando o DLL relevante para o cluster ML Services que você tem. Para o ML Server 9.1, execute o seguinte:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Introduza **1** para selecionar opções **Configurar O Servidor ML para operacionalização**.

1. Introduza **C** para selecionar opção `C. Compute node` . Desta forma, o nó de computação é configurado no nó de trabalho.

1. Saia do Utilitário de Administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Passo 3: Adicione detalhes dos nóns computacional no nó web

Uma vez configurados todos os nós de trabalhador desativados para executar o nó de computação, volte ao nó de borda e adicione os endereços IP dos nós de trabalhador desativados na configuração do nó web ML Server:

1. Aceda através de SSH ao nó de extremidade.

1. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Procure a secção "Uris" e adicione o IP do nó do trabalhador e detalhes do porto.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Passos seguintes

* [Gerir cluster dos Serviços ML no HDInsight](r-server-hdinsight-manage.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções do Armazenamento do Azure para o cluster dos Serviços ML no HDInsight](r-server-storage.md)