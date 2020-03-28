---
title: Tutorial - Use modelos de arranque rápido
description: Aprenda a usar os modelos Azure Quickstart para completar o desenvolvimento do seu modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8747a4614cb8106ce80c6caef0aae36111d2c384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369842"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Tutorial: Use modelos Azure Quickstart

[Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) são um repositório de modelos contribuídos pela comunidade. Pode utilizar os modelos de amostra no desenvolvimento do seu modelo. Neste tutorial, encontra uma definição de recurso do site e adiciona-a ao seu próprio modelo. Leva cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos exportados,](template-tutorial-export-template.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Este modelo funciona para implementar contas de armazenamento e planos de serviço de aplicações, mas você pode querer adicionar um site a ele. Pode utilizar modelos pré-construídos para descobrir rapidamente o JSON necessário para a implementação de um recurso.

## <a name="find-template"></a>Encontrar modelo

1. Modelos [open Azure Quickstart](https://azure.microsoft.com/resources/templates/)
1. Em **Search,** introduza a **aplicação web de linha de utilização do Linux**.
1. Selecione aquele com o título **Implementar uma aplicação web básica do Linux**. Se tiver dificuldade em encontrá-lo, aqui está o [link direto.](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)
1. **Selecione Procurar no GitHub**.
1. Selecione **azuredeploy.json**.
1. Reveja o modelo. Em particular, procure `Microsoft.Web/sites` o recurso.

    ![Site do gestor de recursos quickstart](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Rever o modelo existente

Fundir o modelo de arranque rápido com o modelo existente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

O nome da aplicação web tem de ser único em todo o Azure. Para evitar que os nomes duplicados, a variável **webAppPortalName** foi atualizada a partir de **"webAppPortalName": "[concat('parâmetros('webAppName'), 'webapp')"** para **"webAppPortalName": "[concat('webAppName'), uniqueString (recursoGroup().id)]"**

Adicione uma vírina no `Microsoft.Web/serverfarms` final da definição `Microsoft.Web/sites` para separar a definição de recursos da definição.

Há algumas características importantes a notar neste novo recurso.

Você vai notar que tem um elemento nomeado **depende** que está definido para o plano de serviço de aplicações. Esta definição é necessária porque o plano de serviço de aplicações deve existir antes da aplicação web ser criada. O elemento **dependsOn** diz ao Gestor de Recursos como encomendar os recursos para a implantação.

A propriedade **serverFarmId** utiliza a função [resourceId.](template-functions-resource.md#resourceid) Esta função obtém o identificador único para um recurso. Neste caso, obtém o identificador único para o plano de serviço de aplicações. A aplicação web está associada a um plano específico de serviço de aplicações.

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implementar um modelo.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar um modelo de arranque rápido para o desenvolvimento do seu modelo. No próximo tutorial, adiciona-se tags aos recursos.

> [!div class="nextstepaction"]
> [Adicionar etiquetas](template-tutorial-add-tags.md)
