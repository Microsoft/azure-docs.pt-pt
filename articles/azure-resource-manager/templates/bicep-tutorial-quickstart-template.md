---
title: Tutorial - Use modelos de arranque rápido para o desenvolvimento do Azure Resource Manager Bicep
description: Aprenda a usar os modelos Azure Quickstart para completar o seu desenvolvimento Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632450"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Tutorial: Use modelos Azure Quickstart para desenvolvimento de Bicep gestor de recursos Azure

[Azure Quickstart é](https://azure.microsoft.com/resources/templates/) um repositório de modelos JSON da comunidade. Pode utilizar os modelos de amostra no seu desenvolvimento Bicep. Neste tutorial, encontra uma definição de recursos do site, decompile-la ao Bicep e adicioná-la ao seu ficheiro Bicep. Leva cerca de **12 minutos** para ser completado.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos exportados,](bicep-tutorial-export-template.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Este ficheiro Bicep funciona para implementar contas de armazenamento e planos de serviço de aplicações, mas é melhor adicionar um website ao mesmo. Pode utilizar modelos pré-construídos para descobrir rapidamente o JSON necessário para a implementação de um recurso. Antes de os modelos Azure Quickstart oferecerem amostras bicep, pode utilizar ferramentas de conversão para converter modelos JSON em ficheiros Bicep.

## <a name="find-template"></a>Modelo de descoberta

Atualmente, os modelos Azure Quickstart apenas fornecem modelos JSON. Existem ferramentas que você pode usar para decompiler modelos JSON para modelos Bicep.

1. Abra [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/)
1. Em **Search**, insira _a aplicação web linux_.
1. Selecione o azulejo com o título **Implementar uma aplicação web básica do Linux.** Se tiver problemas em encontrá-lo, aqui está o [link direto.](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)
1. **Selecione Procurar no GitHub**.
1. Selecione _azuredeploy.jsem_. Este é o modelo que pode usar.
1. Selecione **Raw** e, em seguida, faça uma cópia do URL.
1. Navegue para **https://bicepdemo.z22.web.core.windows.net/** , **Selecione Decompile**, e, em seguida, forneça o URL do modelo bruto.
1. Reveja o modelo Bicep. Em particular, procure o `Microsoft.Web/sites` recurso.

    ![Site de arranque rápido do modelo do gestor de recursos](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Existem algumas funcionalidades importantes do Bicep a notar neste novo recurso se tiver trabalhado em modelos JSON.

    Nos modelos ARM JSON, deve especificar manualmente as dependências dos recursos com a propriedade _dependOn._ O recurso do site depende do recurso do plano de serviço de aplicações. Com a Bicep, se você referenciar qualquer propriedade do recurso usando o nome simbólico, a propriedade dependOn é automaticamente adicionada.

    Pode facilmente referenciar o ID do recurso a partir do nome simbólico do plano de serviço de aplicações (appServicePlanName.id) que será traduzido para a função resourceId(...) no modelo JSON compilado.

## <a name="revise-existing-bicep-file"></a>Rever o ficheiro Bicep existente

Misture o modelo de arranque rápido decompilado com o ficheiro Bicep existente. Tal como fez no tutorial anterior, atualize o nome simbólico do recurso e o nome do recurso para corresponder à sua convenção de nomeação.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

O nome da aplicação web tem de ser único em todo o Azure. Para evitar ter nomes duplicados, a `webAppPortalName` variável foi atualizada a partir de `var webAppPortalName_var = '${webAppName}-webapp'` `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Utilize o Azure CLI ou o Azure PowerShell para implementar um modelo Bicep.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a variável **bicepFile** para o caminho para o ficheiro Bicep, como mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
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
  --template-file $bicepFile \
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

Aprendeu a usar um modelo de arranque rápido para o seu desenvolvimento bicep. No próximo tutorial, adiciona-se etiquetas aos recursos.

> [!div class="nextstepaction"]
> [Adicionar etiquetas](bicep-tutorial-add-tags.md)
