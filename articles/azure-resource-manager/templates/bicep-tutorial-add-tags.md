---
title: Tutorial - adicione tags aos recursos no ficheiro Bicep do Azure Resource Manager
description: Adicione etiquetas aos recursos que implementa nos seus ficheiros Bicep. As etiquetas permitem organizar logicamente recursos.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632620"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Tutorial: Adicionar tags em ficheiros Bicep do Gestor de Recursos Azure

Neste tutorial, aprende-se a adicionar tags aos recursos nos seus ficheiros Bicep. [As etiquetas](../management/tag-resources.md) ajudam-no a organizar logicamente os seus recursos. Os valores da etiqueta aparecem nos relatórios de custos. Este tutorial leva **8 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos Quickstart,](bicep-tutorial-quickstart-template.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

O seu ficheiro Bicep anterior implementou uma conta de armazenamento, um plano de Serviço de Aplicações e uma aplicação web.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Depois de implementar estes recursos, poderá ter de rastrear os custos e encontrar recursos que pertençam a uma categoria. Pode adicionar tags para ajudar a resolver estes problemas.

## <a name="add-tags"></a>Adicionar etiquetas

Coloca etiquetas nos recursos para adicionar valores que o ajudam a identificar a sua utilização. Por exemplo, pode adicionar tags que listam o ambiente e o projeto. Pode adicionar tags que identifiquem um centro de custos ou a equipa proprietária do recurso. Adicione os valores que façam sentido para a sua organização.

O exemplo a seguir mostra as alterações no ficheiro Bicep. Copie todo o ficheiro e substitua o seu ficheiro Bicep pelo seu conteúdo.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

É hora de colocar o ficheiro Bicep e ver os resultados.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Selecione um dos recursos, como o recurso da conta de armazenamento. Vê que agora tem etiquetas.

   ![Mostrar tags](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou etiquetas aos recursos. No próximo tutorial, aprenderá a usar ficheiros de parâmetros para simplificar a passagem de valores para a implementação.

> [!div class="nextstepaction"]
> [Use o arquivo de parâmetros](bicep-tutorial-use-parameter-file.md)
