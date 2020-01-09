---
title: Tutorial – usar modelos de início rápido
description: Saiba como usar os modelos de início rápido do Azure para concluir o desenvolvimento do modelo.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 99eb1581c03732691af5eaf9f482ca4ba0605863
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472584"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Tutorial: usar modelos de início rápido do Azure

Os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) são um repositório de modelos contribuídos pela Comunidade. Você pode usar os modelos de exemplo em seu desenvolvimento de modelo. Neste tutorial, você encontrará uma definição de recurso de site e a adicionará ao seu próprio modelo. Leva cerca de **12 minutos** para ser concluída.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre modelos exportados](template-tutorial-export-template.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Esse modelo funciona para implantar contas de armazenamento e planos do serviço de aplicativo, mas talvez você queira adicionar um site a ele. Você pode usar modelos predefinidos para descobrir rapidamente o JSON necessário para implantar um recurso.

## <a name="find-template"></a>Localizar modelo

1. Abrir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)
1. Em **Pesquisar**, insira **implantar aplicativo Web do Linux**.
1. Selecione aquele com o título **implantar um aplicativo Web Linux básico**. Se você tiver problemas para encontrá-lo, aqui está o [link direto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecione **Procurar no GitHub**.
1. Selecione **azuredeploy. JSON**.
1. Examine o modelo. Em particular, procure o recurso `Microsoft.Web/sites`.

    ![Site de início rápido do modelo do Resource Manager](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Revisar modelo existente

Mescle o modelo de início rápido com o modelo existente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

O nome do aplicativo Web precisa ser exclusivo no Azure. Para evitar a existência de nomes duplicados, a variável **webAppPortalName** foi atualizada de **"webAppPortalName": "[Concat (Parameters (' webappname '), '-webapp ')]"** para **"webAppPortalName": "[Concat (Parameters (' webapp '), uniquestring (resourcegroup (). ID))]"** .

Adicione uma vírgula ao final da definição de `Microsoft.Web/serverfarms` para separar a definição de recurso da definição de `Microsoft.Web/sites`.

Há alguns recursos importantes a serem observados nesse novo recurso.

Você notará que ele tem um elemento chamado **depends** que está definido como o plano do serviço de aplicativo. Essa configuração é necessária porque o plano do serviço de aplicativo deve existir antes que o aplicativo Web seja criado. O elemento **depends** informa ao Resource Manager como ordenar os recursos para implantação.

A propriedade **serverFarmId** usa a função [ResourceId](template-functions-resource.md#resourceid) . Essa função obtém o identificador exclusivo de um recurso. Nesse caso, ele obtém o identificador exclusivo para o plano do serviço de aplicativo. O aplicativo Web está associado a um plano específico do serviço de aplicativo.

## <a name="deploy-template"></a>Implementar o modelo

Use CLI do Azure ou Azure PowerShell para implantar um modelo.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a usar um modelo de início rápido para o desenvolvimento de modelo. No próximo tutorial, você adicionará marcas aos recursos.

> [!div class="nextstepaction"]
> [Adicionar marcas](template-tutorial-add-tags.md)
