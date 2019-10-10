---
title: Criar e implantar um modelo de Azure Resource Manager usando a ideia IntelliJ | Microsoft Docs
description: Saiba como criar seu primeiro modelo de Azure Resource Manager usando a ideia IntelliJ e como implantá-lo.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: d25a5720e3aaa8ad6306a72d2db8d3ca2e89861b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169452"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Início rápido: criar e implantar modelos de Azure Resource Manager usando a ideia IntelliJ

Saiba como implantar um modelo do Resource Manager no Azure usando a ideia IntelliJ e o processo de edição e atualização do modelo diretamente do IDE. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md).

![Diagrama do portal de início rápido do modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Depois de concluir o tutorial, você implantará uma conta de armazenamento do Azure. O mesmo processo pode ser usado para implantar outros recursos do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Uma [INTELLIJ Idea](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition instalada
* O [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) instalado, consulte o [Guia de gerenciamento de plugins do IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html) para obter mais informações
* Esteja [conectado](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) à sua conta do Azure para o Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Implantar um modelo de início rápido

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Os modelos de início rápido do Azure são um repositório para modelos do Resource Manager. O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Ele define um recurso de conta de armazenamento do Azure.

1. Clique com o botão direito do mouse e salve o [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) e [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) em seu computador local.

1. Se o kit de ferramentas do Azure estiver instalado e conectado corretamente, você deverá ver o Azure Explorer na barra lateral da ideia IntelliJ. Clique com o botão direito do mouse no **Gerenciamento de recursos** e selecione **criar implantação**.

    ![Modelo do Resource Manager clique com o botão direito do mouse para criar a implantação](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Configurar seu **nome de implantação**, **assinatura**, **grupo de recursos**e **região**. Aqui, implantamos o modelo em um novo grupo de recursos `testRG`. Em seguida, selecione caminho para o **modelo de recurso** como `azuredeploy.json` e **parâmetros de recurso** como `azuredeploy.parameters.json` que você baixou.

    ![Modelo do Resource Manager selecionar arquivos para criar a implantação](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Depois que você clicar em OK, a implantação será iniciada. Até a conclusão da implantação, você poderá encontrar o progresso na barra de **status** da ideia IntelliJ na parte inferior.

    ![Status de implantação do modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Procurar uma implantação existente

1. Depois que a implantação for concluída, você poderá ver o novo grupo de recursos `testRG` e uma nova implantação criada. Clique com o botão direito do mouse na implantação e você poderá ver uma lista de possíveis ações. Agora, selecione **mostrar propriedades**.

    ![Implantação de busca de modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Uma exibição de guia será aberta para mostrar algumas propriedades úteis, como o status da implantação e a estrutura do modelo.

    ![Modelo do Resource Manager mostrar propriedades de implantação](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Editar e atualizar uma implantação existente

1. Selecione **Editar implantação** no menu do botão direito do mouse ou no modo de exibição mostrar propriedades antes de. Outra exibição de guia será aberta, mostrando os arquivos de modelo e parâmetro para a implantação no Azure. Para salvar esses arquivos no local, você pode clicar em **Exportar arquivo de modelo** ou **exportar arquivos de parâmetro**.

    ![Implantação de edição de modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Você pode editar os dois arquivos nesta página e implantar as alterações no Azure. Aqui, modificamos o valor de **storageAccountType** em arquivos de parâmetro, de `Standard_LRS` para `Standard_GRS`. Em seguida, clique em **Atualizar implantação** na parte inferior e confirme a atualização.

    ![Implantação de edição de modelo do Resource Manager](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Após a conclusão da implantação da atualização, você pode verificar no portal que a conta de armazenamento criada foi alterada para `Standard_GRS`.

## <a name="clean-up-resources"></a>Limpar recursos

1. Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Você pode fazer isso em portal do Azure ou CLI do Azure. No Azure Explorer da IntelliJ IDEA, clique com o botão direito do mouse no **grupo de recursos** criado e selecione Excluir.

    ![Excluir grupo de recursos no Azure Explorer do IntelliJ IDEA](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Observe que a exclusão de uma implantação não excluirá os recursos criados pela implantação. Exclua o grupo de recursos ou recursos específicos correspondentes se você não precisar mais deles.

## <a name="next-steps"></a>Passos seguintes

O foco principal deste guia de início rápido é usar a ideia IntelliJ para implantar um modelo existente de modelos de início rápido do Azure. Você também aprendeu como exibir e atualizar uma implantação existente no Azure. Os modelos de Início Rápido do Azure poderão não fornecer-lhe tudo o que precisa. Para saber mais sobre o desenvolvimento de modelos, consulte nossa nova série de tutoriais iniciantes:

> [!div class="nextstepaction"]
> [Tutoriais para iniciantes](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Visite o Java no centro de desenvolvimento do Azure](https://docs.microsoft.com/azure/java)
