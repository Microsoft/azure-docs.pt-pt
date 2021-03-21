---
title: Tutorial - Use modelos de arranque rápido
description: Aprenda a usar modelos Azure Quickstart para completar o desenvolvimento do seu modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106908"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Tutorial: Use modelos de arranque rápido Azure

[Azure Quickstart é](https://azure.microsoft.com/resources/templates/) um repositório de modelos de contribuições comunitárias. Pode utilizar os modelos de amostra no desenvolvimento do seu modelo. Neste tutorial, você encontra uma definição de recursos do site, e adicione-a ao seu próprio modelo. Leva cerca de **12 minutos** para ser completado.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos exportados,](template-tutorial-export-template.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Este modelo funciona para implementar contas de armazenamento e planos de serviço de aplicações, mas é melhor adicionar um website ao mesmo. Pode utilizar modelos pré-construídos para descobrir rapidamente o JSON necessário para a implementação de um recurso.

## <a name="find-template"></a>Modelo de descoberta

1. Abra [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/)
1. Em **Search**, insira _a aplicação web linux_.
1. Selecione o azulejo com o título **Implementar uma aplicação web básica do Linux.** Se tiver problemas em encontrá-lo, aqui está o [link direto.](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)
1. **Selecione Procurar no GitHub**.
1. Selecione _azuredeploy.jsem_.
1. Reveja o modelo. Em particular, procure o `Microsoft.Web/sites` recurso.

    ![Site de arranque rápido do modelo do gestor de recursos](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Rever o modelo existente

Fundir o modelo de arranque rápido com o modelo existente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

O nome da aplicação web tem de ser único em todo o Azure. Para evitar ter nomes duplicados, a `webAppPortalName` variável foi atualizada a partir de `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Adicione uma vírgula no final da `Microsoft.Web/serverfarms` definição para separar a definição de recurso da `Microsoft.Web/sites` definição.

Há algumas características importantes a notar neste novo recurso.

Você vai notar que tem um elemento nomeado `dependsOn` que está definido para o plano de serviço de aplicações. Esta definição é necessária porque o plano de serviço de aplicações deve existir antes da criação da aplicação web. O `dependsOn` elemento diz ao Gestor de Recursos como encomendar os recursos para a implantação.

A `serverFarmId` propriedade utiliza a função [resourceId.](template-functions-resource.md#resourceid) Esta função obtém o identificador único para um recurso. Neste caso, obtém o identificador único para o plano de serviço de aplicações. A aplicação web está associada a um plano específico de serviço de aplicações.

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implementar um modelo.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a usar um modelo de arranque rápido para o desenvolvimento do seu modelo. No próximo tutorial, adiciona-se etiquetas aos recursos.

> [!div class="nextstepaction"]
> [Adicionar etiquetas](template-tutorial-add-tags.md)
