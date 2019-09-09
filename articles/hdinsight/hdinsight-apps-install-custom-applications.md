---
title: Instalar seus próprios aplicativos de Apache Hadoop personalizados no Azure HDInsight
description: Saiba como instalar aplicativos do HDInsight para Apache Hadoop clusters no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 22a2eddcd94f6082d248450d825c5ba12b31571e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810338"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos de Apache Hadoop personalizados no Azure HDInsight

Neste artigo, você aprenderá a instalar um aplicativo [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight, que não foi publicado no portal do Azure. A aplicação que irá instalar neste artigo é a [Hue](https://gethue.com/).

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux.  Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si.  

Outros artigos relacionados:

* [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo do HDInsight em seus clusters.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [INGLÊS Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do HDInsight: Saiba como definir aplicativos do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
Se pretender instalar aplicações do HDInsight num cluster do HDInsight existente, tem de ter um cluster do HDInsight. Para criar um, consulte [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Também pode instalar aplicações do HDInsight ao criar um cluster do HDInsight.

## <a name="install-hdinsight-applications"></a>Instalar aplicações do HDInsight
As aplicações do HDInsight podem ser instaladas quando cria um cluster ou num cluster do HDInsight existente. Para definir modelos de Azure Resource Manager, [consulte msdn: Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do HDInsight.

Ficheiros necessários para implementar esta aplicação (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): O modelo do Resource Manager para instalar o aplicativo HDInsight. Consulte [MSDN: Instale um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx) do HDInsight para desenvolver seu próprio modelo do Resource Manager.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): A ação de script sendo chamada pelo modelo do Resource Manager para configurar o nó de borda.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O arquivo binário de matiz sendo chamado de Hui-install_v0. sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): O arquivo binário de matiz sendo chamado de Hui-install_v0. sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Um aplicativo Web de exemplo (Tomcat) que está sendo chamado de Hui-install_v0. sh.

**Para instalar a Hue num cluster do HDInsight existente**

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botão abre um modelo do Azure Resource Manager no Portal do Azure.  O modelo do Resource Manager está localizado [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)em.  Para saber como escrever esse modelo do Resource Manager, consulte [MSDN: Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do HDInsight.
2. No painel **Parâmetros**, introduza o seguinte:

   * **ClusterName**: Insira o nome do cluster no qual você deseja instalar o aplicativo. Este cluster tem de ser um cluster existente.
3. Clique em **OK** para guardar os parâmetros.
4. No painel **Implementação personalizada**, introduza **Grupo de recursos**.  O grupo de recursos é um contentor que agrupa o cluster, a conta do Storage dependente e outros recursos. É necessário utilizar o mesmo grupo de recursos do cluster.
5. Clique em **Termos legais** e em **Criar**.
6. Verifique se a caixa de verificação **Afixar ao dashboard** está selecionada e, em seguida, clique em **Criar**. Pode ver o estado da instalação no mosaico afixado ao dashboard do portal e a notificação do portal (clique no ícone de sino na parte superior do portal).  A instalação da aplicação demora cerca de 10 minutos.

**Para instalar a Hue ao criar um cluster**

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botão abre um modelo do Azure Resource Manager no Portal do Azure.  O modelo do Resource Manager está localizado [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)em.  Para saber como escrever esse modelo do Resource Manager, consulte [MSDN: Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do HDInsight.
2. Siga a instrução para criar o cluster e instalar a Hue. Para obter mais informações sobre a criação de clusters do HDInsight, consulte [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Além do portal do Azure, você também pode usar o [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) e a [CLI clássica do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) para chamar modelos do Resource Manager.

## <a name="validate-the-installation"></a>Validar a instalação
Pode verificar o estado da aplicação no Portal do Azure para validar a instalação da aplicação. Além disso, também pode validar se todos os pontos finais de HTTP surgiram conforme esperado e a página Web se for o caso:

**Para abrir o portal da Hue**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique no cluster onde instalou a aplicação.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. Deverá ver **hue** indicada no painel **Aplicações Instaladas**.
5. Clique em **hue** na lista para ver as propriedades.  
6. Clique no link da página da Web para validar o site; Abra o ponto de extremidade HTTP em um navegador para validar a interface do usuário da Web do matiz, abra o ponto de extremidade SSH usando SSH. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Resolver problemas relacionados com a instalação
Pode verificar o estado da instalação da aplicação na notificação do portal (clique no ícone de sino na parte superior do portal).

Em caso de falha na instalação de uma aplicação, pode ver as mensagens de erro e as informações de depuração em 3 locais:

* Aplicações do HDInsight: informações de erro gerais.

    Abra o cluster a partir do portal e clique em Aplicações no painel Definições:

    ![aplicações do hdinsight erro de instalação da aplicação](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* Ação de script do HDInsight: Se a mensagem de erro dos aplicativos do HDInsight indicar uma falha de ação de script, mais detalhes sobre a falha de script serão apresentados no painel Ações de script.

    Clique em Ação de Script no painel Definições. O histórico de ações de script apresenta as mensagens de erro

    ![aplicações do hdinsight erro de ação de script](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Interface do usuário da Web do amAmbari: Se o script de instalação foi a causa da falha, use a interface do usuário da Web do amAmbari para verificar os logs completos sobre os scripts de instalação.

    Para obter mais informações, consulte [Resolução de problemas](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Remover aplicações do HDInsight
Existem várias formas de eliminar aplicações de HDInsight.

### <a name="use-portal"></a>Utilizar o portal
**Para remover uma aplicação através do portal**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda.  Se o mesmo não for apresentado, clique em **Procurar** e, em seguida, em **Clusters do HDInsight**.
3. Clique no cluster onde instalou a aplicação.
4. No painel **Definições**, clique em **aplicações** na categoria **Geral**. Deverá ver uma lista da aplicação instalada. Neste artigo, **matiz** listado na folha **aplicativos instalados** .
5. Clique com o botão direito do rato na aplicação que pretende remover e, em seguida, clique em **Eliminar**.
6. Clique em **Sim** para confirmar.

No portal, também pode eliminar o cluster ou eliminar o grupo de recursos que contém a aplicação.

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Através do Azure PowerShell, pode eliminar o cluster ou o grupo de recursos. Consulte [Eliminar clusters ao utilizar o Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Com a CLI do Azure, pode eliminar o cluster ou o grupo de recursos. Consulte [Eliminar clusters ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Passos seguintes
* [INGLÊS Instalar um aplicativo](https://msdn.microsoft.com/library/mt706515.aspx)do hdinsight: saiba como desenvolver modelos do Resource Manager para implantar aplicativos do hdinsight.
* [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo do HDInsight em seus clusters.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Resource Manager para criar clusters do HDInsight.
* [Utilizar nós de extremidade vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como utilizar um nó de extremidade vazio para aceder ao cluster do HDInsight, testar aplicações do HDInsight e alojar aplicações do HDInsight.
