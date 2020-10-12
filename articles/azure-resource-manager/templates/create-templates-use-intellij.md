---
title: Modelo de implementação - IntelliJ IDEA
description: Aprenda a criar o seu primeiro modelo de Gestor de Recursos Azure utilizando o IntelliJ IDEA e como implementá-lo.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: 6a596eff70bfc20b5a6752ef47398bd14d527c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973331"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Criar e implementar modelos ARM utilizando o IntelliJ IDEA

Aprenda a implementar um modelo Azure Resource Manager (ARM) para Azure utilizando o IntelliJ IDEA e o processo de edição e atualização do modelo diretamente do IDE. Os modelos ARM são ficheiros JSON que definem os recursos necessários para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções Azure, consulte a [visão geral da implementação do modelo.](overview.md)

![Diagrama do portal do modelo do gestor de recursos](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de completar o tutorial, você implanta uma conta de Armazenamento Azure. O mesmo processo pode ser usado para implantar outros recursos Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* As Edições [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate ou Community instaladas
* O [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) instalado; obtenha mais informações no [guia de gestão de plug-ins do IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html).
* Ter [sessão iniciada](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) na conta do Azure do Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Implemente um modelo quickstart

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates é um repositório para modelos ARM. O modelo utilizado neste artigo chama-se [Criar uma conta de armazenamento padrão.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) Define um recurso de conta Azure Storage.

1. Clique à direita e guarde o [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) e para o seu computador [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) local.

1. Se o seu Azure Toolkit estiver corretamente instalado e assinado, deverá ver o Azure Explorer na barra lateral do IntelliJ IDEA. Clique com o botão direito na **Gestão de Recursos** e selecione **Criar Implementação**.

    ![Clique direito do modelo do gestor de recursos para criar implementação](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Config o seu **Nome de Implantação,** **Assinatura,** **Grupo de Recursos**e **Região**. Aqui implantamos o modelo num novo grupo de `testRG` recursos. Em seguida, selecione o caminho para **o modelo de recurso** como e `azuredeploy.json` **parâmetros de recursos** à medida que `azuredeploy.parameters.json` descarregou.

    ![O modelo de gestor de recursos seleciona ficheiros para criar a implementação](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Depois de clicar em OK, a implementação é iniciada. Até que a implementação esteja concluída, pode encontrar o progresso da barra de **estado** da IntelliJ IDEA na parte inferior.

    ![Estado de implementação do modelo do gestor de recursos](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Navegue por uma implantação existente

1. Depois de a implementação ser feita, pode ver o novo grupo de recursos `testRG` e uma nova implementação criada. Clique com o botão direito na implementação e pode ver uma lista de possíveis ações. Agora selecione **Show Properties**.

    ![Implementação de modelo de gestão de recursos](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Uma vista de separador estará aberta para mostrar algumas propriedades úteis, como o estado de implantação e a estrutura do modelo.

    ![O modelo do gestor de recursos mostra propriedades de implementação](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Editar e atualizar uma implementação existente

1. Selecione **Editar Implementação** a partir do menu de clique direito ou a visualização das propriedades do show antes. Outra visão do separador estará aberta, mostrando o modelo e os ficheiros de parâmetros para a implementação no Azure. Para guardar esses ficheiros para locais, pode clicar em **Ficheiros de Modelo de Exportação**  ou **Ficheiros de Parâmetros de Exportação**.

    ![Implementação de edição de modelo de gestor de recursos](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Pode editar os dois ficheiros nesta página e implementar as alterações no Azure. Aqui modificamos o valor do **armazenamentoAcolho** em ficheiros de parâmetros, de `Standard_LRS` `Standard_GRS` . Em seguida, clique em **Atualizar a Implementação** na parte inferior e confirme a atualização.

    ![A screenshot mostra o modelo de Gestor de Recursos com a pronta de atualização visualizada.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Após a implementação da atualização concluída, pode verificar no portal que a conta de armazenamento criada é alterada para `Standard_GRS` .

## <a name="clean-up-resources"></a>Limpar os recursos

1. Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Pode fazê-lo a partir do portal Azure ou do Azure CLI. No Azure Explorer de IntelliJ IDEA, clique em clique no seu **grupo de recursos** criado e selecione excluir.

    ![Eliminar grupo de recursos no Azure Explorer da IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Note que eliminar uma implantação não eliminará os recursos criados pela implantação. Por favor, elimine o grupo de recursos correspondente ou recursos específicos se já não precisar deles.

## <a name="next-steps"></a>Passos seguintes

O principal foco deste artigo é usar o IntelliJ IDEA para implementar um modelo existente a partir de modelos Azure Quickstart. Também aprendeu a ver e atualizar uma implementação existente no Azure. Os modelos de Início Rápido do Azure poderão não fornecer-lhe tudo o que precisa. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiantes:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Visite Java no centro de Azure Dev](/azure/java)
