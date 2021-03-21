---
title: Instale as suas aplicações personalizadas Apache Hadoop no Azure HDInsight
description: Saiba como instalar aplicações HDInsight para clusters Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 9591551b7d5657803e770bd71b47acb141b40fb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942690"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instale aplicações personalizadas da Apache Hadoop no Azure HDInsight

Neste artigo, você vai aprender a instalar uma aplicação [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight, que não foi publicado no portal Azure. A aplicação que vai instalar neste artigo é [Hue.](https://gethue.com/)

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster HDInsight.  Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

## <a name="prerequisites"></a>Pré-requisitos

Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-hdinsight-applications"></a>Instalar aplicações do HDInsight

As aplicações do HDInsight podem ser instaladas quando cria um cluster ou num cluster do HDInsight existente. Para definir modelos do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application).

Ficheiros necessários para implementar esta aplicação (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modelo do Azure Resource Manager para a instalação da aplicação do HDInsight. Consulte [MSDN: instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application) para desenvolver o seu próprio modelo do Azure Resource Manager.
* [hue install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): a ação de Script a ser chamada pelo modelo do Azure Resource Manager para a configuração do nó de extremidade.
* [hue binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui-install_v0.sh.
* [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Uma aplicação Web de exemplo (Tomcat) a ser chamada a partir de hui install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Para instalar a Hue num cluster do HDInsight existente

1. Selecione a seguinte imagem para iniciar seduca e abra o modelo de Gestor de Recursos no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo de Gestor de Recursos está localizado em [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application).

1. Selecione o **grupo de Recursos** existente que contém o seu cluster a partir da lista de drop-down. É necessário usar o mesmo grupo de recursos que o cluster.

1. Insira o nome do cluster onde pretende instalar a aplicação. Este cluster tem de ser um cluster existente.

1. Selecione a caixa de verificação pois **concordo com os termos e condições acima indicados**.

1. Selecione **Comprar**.

Pode ver o estado da instalação no mosaico afixado ao dashboard do portal e a notificação do portal (clique no ícone de sino na parte superior do portal).  A instalação da aplicação demora cerca de 10 minutos.

### <a name="to-install-hue-while-creating-a-cluster"></a>Para instalar a Hue ao criar um cluster

1. Selecione a seguinte imagem para iniciar seduca e abra o modelo de Gestor de Recursos no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo de Gestor de Recursos está localizado em [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application).

2. Siga a instrução para criar o cluster e instalar a Hue. Para obter mais informações sobre a criação de clusters do HDInsight, consulte [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Outros métodos de instalação

Além de Portal do Azure, também pode utilizar o [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) e a [CLI do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) para chamar modelos do Azure Resource Manager.

## <a name="validate-the-installation"></a>Validar a instalação

Pode verificar o estado da aplicação no Portal do Azure para validar a instalação da aplicação. Além disso, também pode validar todos os pontos finais HTTP surgindo como esperado e a página web se houver um.

Para **Hue,** pode utilizar os seguintes passos:

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde instalou a aplicação.
1. A partir do menu **Definições,** selecione **Aplicações**.
1. Selecione **a tonalidade** da lista para ver as propriedades.  
1. Selecione o link da página web para validar o site.

### <a name="azure-cli"></a>CLI do Azure

`CLUSTERNAME`Substitua, e `RESOURCEGROUP` pelos valores relevantes e, em seguida, insira os comandos abaixo:

* Para listar todas as aplicações para o cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Para recuperar propriedades da aplicação especificada.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Resolver problemas relacionados com a instalação

Pode verificar o estado da instalação da aplicação na notificação do portal (clique no ícone de sino na parte superior do portal).

Se uma instalação de aplicação falhar, pode ver as mensagens de erro e depurar informações de três locais:

* Aplicações do HDInsight: informações de erro gerais.

    Abra o cluster a partir do portal e selecione Aplicações a partir de Definições:

    ![aplicações do hdinsight erro de instalação da aplicação](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Ação de script do HDInsight: Se a mensagem de erro das Aplicações do HDInsight indicar uma falha de ação de script, serão apresentados mais detalhes sobre a falha de script no painel de ações de script.

    Selecione Ação do Script a partir de Definições. O histórico de ações de script apresenta as mensagens de erro

    ![aplicações do hdinsight erro de ação de script](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI: Se o script de instalação foi a causa da falha, use a Ambari Web UI para verificar registos completos sobre os scripts de instalação.

    Para obter mais informações, consulte [as ações do script Troubleshoot](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Remover aplicações do HDInsight

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde instalou a aplicação.
1. A partir do menu **Definições,** selecione **Aplicações**.
1. Clique com o botão direito na aplicação que pretende remover e, em seguida, **selecione Delete**.
1. Selecione **Sim** para confirmar.

### <a name="azure-cli"></a>CLI do Azure

`NAME` `CLUSTERNAME` Substitua, e pelos `RESOURCEGROUP` valores relevantes e, em seguida, insira o comando abaixo:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Passos seguintes

* [MSDN: instalar uma aplicação do HDInsight](/rest/api/hdinsight/hdinsight-application): saiba como desenvolver modelos do Azure Resource Manager para a implementação de aplicações do HDInsight.
* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters Apache Hadoop baseados em Linux em HDInsight usando modelos de Gestor de Recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a chamar modelos de Gestor de Recursos para criar clusters HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.
