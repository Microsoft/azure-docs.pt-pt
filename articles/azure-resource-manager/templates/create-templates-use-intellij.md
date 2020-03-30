---
title: Modelo de implementação - IntelliJ IDEA
description: Aprenda a criar o seu primeiro modelo de Gestor de Recursos Azure usando o IntelliJ IDEA e como implementá-lo.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153357"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Criar e implementar modelos ARM utilizando o IntelliJ IDEA

Aprenda a implementar um modelo de Gestor de Recursos Azure (ARM) para o Azure utilizando o IntelliJ IDEA, e o processo de edição e atualização do modelo diretamente do IDE. Os modelos ARM são ficheiros JSON que definem os recursos necessários para implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções Azure, consulte a visão geral da [implementação](overview.md)do modelo .

![Diagrama do portal do modelo do gestor de recursos](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de concluir o tutorial, implementa uma conta de Armazenamento Azure. O mesmo processo pode ser utilizado para implantar outros recursos Azure.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* As Edições [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate ou Community instaladas
* O [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) instalado; obtenha mais informações no [guia de gestão de plug-ins do IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html).
* Ter [sessão iniciada](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) na conta do Azure do Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Implementar um modelo Quickstart

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Os modelos Azure Quickstart é um repositório para modelos ARM. O modelo utilizado neste artigo chama-se [Criar uma conta de armazenamento padrão.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) Define um recurso de conta Azure Storage.

1. Clique à direita [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) e [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) guarde o e para o seu computador local.

1. Se o seu Kit de Ferramentas Azure estiver devidamente instalado e inscrito, deverá ver o Azure Explorer na barra lateral do IntelliJ IDEA. Clique à direita na **Gestão** de Recursos e selecione **Criar implementação**.

    ![Modelo de gestor de recursos clique direito para criar implementação](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Config o seu nome de **implantação,** **subscrição,** **Grupo de Recursos,** e **Região**. Aqui implantamos o modelo `testRG`num novo grupo de recursos. Em seguida, selecione `azuredeploy.json` o caminho para o Modelo de **Recurso** como e **paramos de Recursos** à medida `azuredeploy.parameters.json` que descarregou.

    ![Modelo de Gestor de Recursos seleciona ficheiros para criar implementação](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Depois de clicar em OK, a implementação está iniciada. Até que a implementação esteja concluída, pode encontrar o progresso da barra de **estado** do IntelliJ IDEA na parte inferior.

    ![Estado de implementação do modelo do Gestor de Recursos](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Navegue por uma implementação existente

1. Após a implementação, pode ver o `testRG` novo grupo de recursos e uma nova implementação criada. Clique na direita na implementação e pode ver uma lista de possíveis ações. Agora selecione **Mostrar Propriedades**.

    ![Modelo de gestor de recursos navega implementação](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Uma vista para o separador estará aberta para mostrar algumas propriedades úteis, como o estado de implementação e a estrutura do modelo.

    ![Modelo de gestor de recursos mostrar propriedades de implementação](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Editar e atualizar uma implementação existente

1. **Selecione Editar Implementação** a partir do menu de clique direito ou a visualização das propriedades do show antes. Outra vista para o separador estará aberta, mostrando o modelo e os ficheiros de parâmetros para a implementação no Azure. Para guardar esses ficheiros para o local, pode clicar em Ficheiros de **Modelode Exportação** ou **Ficheiros de Parâmetros de Exportação**.

    ![Implementação de edição de modelo de gestor de recursos](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Pode editar os dois ficheiros nesta página e implementar as alterações para o Azure. Aqui modificamos o valor do **armazenamentoAccountType** `Standard_LRS` em `Standard_GRS`ficheiros de parâmetros, de para . Em seguida, clique em **'Actualizar's Deployment** na parte inferior e confirmar a atualização.

    ![Implementação de edição de modelo de gestor de recursos](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Após a implementação da atualização concluída, pode verificar no `Standard_GRS`portal que a conta de armazenamento criada é alterada para .

## <a name="clean-up-resources"></a>Limpar recursos

1. Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Pode fazê-lo a partir do portal Azure ou do Azure CLI. No Azure Explorer do IntelliJ IDEA, clique no seu grupo de **recursos** criado e selecione eliminar.

    ![Eliminar grupo de recursos no Azure Explorer do IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Note que eliminar uma implementação não eliminará os recursos criados pela implantação. Por favor, elimine o grupo de recursos correspondente ou recursos específicos se já não precisar deles.

## <a name="next-steps"></a>Passos seguintes

O foco principal deste artigo é usar o IntelliJ IDEA para implementar um modelo existente a partir de modelos Azure Quickstart. Também aprendeu a ver e atualizar uma implementação existente no Azure. Os modelos de Início Rápido do Azure poderão não fornecer-lhe tudo o que precisa. Para saber mais sobre o desenvolvimento do modelo, consulte a nossa nova série tutorial de principiante:

> [!div class="nextstepaction"]
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Visite Java no centro Azure Dev](https://docs.microsoft.com/azure/java)
