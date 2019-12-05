---
title: Instalar seus aplicativos de Apache Hadoop personalizados no Azure HDInsight
description: Saiba como instalar aplicativos do HDInsight para Apache Hadoop clusters no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c109f5309837de8c9b4bd3e4bc5a5da0a6da534e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806869"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos de Apache Hadoop personalizados no Azure HDInsight

Neste artigo, você aprenderá a instalar um aplicativo [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight, que não foi publicado no portal do Azure. O aplicativo que você instalará neste artigo é o [matiz](https://gethue.com/).

Uma aplicação do HDInsight é um aplicativo que os utilizadores podem instalar num cluster do HDInsight.  Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

## <a name="prerequisites"></a>Pré-requisitos

Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-hdinsight-applications"></a>Instalar aplicações do HDInsight

As aplicações do HDInsight podem ser instaladas quando cria um cluster ou num cluster do HDInsight existente. Para definir modelos do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Ficheiros necessários para implementar esta aplicação (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modelo do Azure Resource Manager para a instalação da aplicação do HDInsight. Consulte [MSDN: instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) para desenvolver o seu próprio modelo do Azure Resource Manager.
* [hue install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): a ação de Script a ser chamada pelo modelo do Azure Resource Manager para a configuração do nó de extremidade.
* [hue binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O ficheiro binário da Hue a ser chamado a partir de hui-install_v0.sh.
* [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Uma aplicação Web de exemplo (Tomcat) a ser chamada a partir de hui install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Para instalar a Hue num cluster do HDInsight existente

1. Selecione a imagem a seguir para entrar no Azure e abra o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Resource Manager está localizado em [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

1. Selecione o **grupo de recursos** existente que contém o cluster na lista suspensa. É necessário usar o mesmo grupo de recursos que o cluster.

1. Insira o nome do cluster no qual você deseja instalar o aplicativo. Este cluster tem de ser um cluster existente.

1. Marque a caixa de seleção para **eu concordo com os termos e condições declarados acima**.

1. Selecione **Comprar**.

Pode ver o estado da instalação no mosaico afixado ao dashboard do portal e a notificação do portal (clique no ícone de sino na parte superior do portal).  A instalação da aplicação demora cerca de 10 minutos.

### <a name="to-install-hue-while-creating-a-cluster"></a>Para instalar a Hue ao criar um cluster

1. Selecione a imagem a seguir para entrar no Azure e abra o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Resource Manager está localizado em [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Para obter informações sobre como escrever este modelo do Azure Resource Manager, consulte [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

2. Siga a instrução para criar o cluster e instalar a Hue. Para obter mais informações sobre a criação de clusters do HDInsight, consulte [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Outros métodos de instalação

Além de Portal do Azure, também pode utilizar o [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) e a [CLI do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) para chamar modelos do Azure Resource Manager.

## <a name="validate-the-installation"></a>Validar a instalação

Pode verificar o estado da aplicação no Portal do Azure para validar a instalação da aplicação. Além disso, você também pode validar todos os pontos de extremidade HTTP fornecidos conforme o esperado e a página da Web, se houver.

Para **matiz**, você pode usar as seguintes etapas:

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde você instalou o aplicativo.
1. No menu **configurações** , selecione **aplicativos**.
1. Selecione **matiz** na lista para exibir as propriedades.  
1. Selecione o link da página da Web para validar o site.

### <a name="azure-cli"></a>CLI do Azure

Substitua `CLUSTERNAME`e `RESOURCEGROUP` pelos valores relevantes e, em seguida, insira os comandos abaixo:

* Para listar todos os aplicativos para o cluster HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Para recuperar as propriedades do aplicativo especificado.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Resolver problemas relacionados com a instalação

Pode verificar o estado da instalação da aplicação na notificação do portal (clique no ícone de sino na parte superior do portal).

Se a instalação de um aplicativo falhar, você poderá ver as mensagens de erro e depurar as informações de três locais:

* Aplicações do HDInsight: informações de erro gerais.

    Abra o cluster no portal e selecione aplicativos em configurações:

    ![aplicações do hdinsight erro de instalação da aplicação](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Ação de script do HDInsight: Se a mensagem de erro das Aplicações do HDInsight indicar uma falha de ação de script, serão apresentados mais detalhes sobre a falha de script no painel de ações de script.

    Selecione Ação de script nas configurações. O histórico de ações de script apresenta as mensagens de erro

    ![aplicações do hdinsight erro de ação de script](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Interface do usuário da Web do Apache Ambari: se o script de instalação foi a causa da falha, use a interface do usuário da Web do amAmbari para verificar os logs completos sobre os scripts de instalação.

    Para obter mais informações, consulte [Resolução de problemas](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Remover aplicações do HDInsight

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o cluster onde você instalou o aplicativo.
1. No menu **configurações** , selecione **aplicativos**.
1. Clique com o botão direito do mouse no aplicativo que você deseja remover e selecione **excluir**.
1. Selecione **Sim** para confirmar.

### <a name="azure-cli"></a>CLI do Azure

Substitua `NAME`, `CLUSTERNAME`e `RESOURCEGROUP` pelos valores relevantes e, em seguida, insira o comando a seguir:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Passos seguintes

* [MSDN: instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como desenvolver modelos do Azure Resource Manager para a implementação de aplicações do HDInsight.
* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Resource Manager para criar clusters do HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.
