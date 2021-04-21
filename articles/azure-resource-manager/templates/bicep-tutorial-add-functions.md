---
title: Tutorial - adicione funções aos ficheiros Bicep do Azure Resource Manager
description: Adicione funções aos seus ficheiros Bicep para construir valores.
author: mumian
ms.date: 04/20/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 43dec6ceb21a6604bc0034b3f14b79ffd2cbe263
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773806"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar funções ao ficheiro Bicep do Gestor de Recursos da Azure

Neste tutorial, aprende-se a adicionar [funções de modelo](template-functions.md) ao seu ficheiro Bicep. Utiliza-se funções para construir valores dinâmicos. Atualmente, o Bicep não suporta funções definidas pelo utilizador. Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre parâmetros,](bicep-tutorial-add-parameters.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

A localização da conta de armazenamento é codificada para os **EUA.** No entanto, poderá ser necessário implantar a conta de armazenamento noutras regiões. Estás novamente a enfrentar um problema do teu ficheiro Bicep sem flexibilidade. Pode adicionar um parâmetro para a localização, mas seria ótimo se o seu valor padrão fizesse mais sentido do que apenas um valor codificado.

## <a name="use-function"></a>Função de utilização

As funções adicionam flexibilidade ao seu ficheiro Bicep obtendo valores dinamicamente durante a implementação. Neste tutorial, você usa uma função para obter a localização do grupo de recursos que você está usando para implantação.

O exemplo a seguir mostra as alterações para adicionar um parâmetro chamado `location` . O valor predefinido do parâmetro chama a função [grupo de recursos.](template-functions-resource.md#resourcegroup) Esta função devolve um objeto com informações sobre o grupo de recursos que está a ser utilizado para a implantação. Uma das propriedades do objeto é uma propriedade de localização. Quando utiliza o valor predefinido, a localização da conta de armazenamento tem a mesma localização que o grupo de recursos. Os recursos dentro de um grupo de recursos não têm que partilhar o mesmo local. Também pode fornecer uma localização diferente quando necessário.

Copie todo o ficheiro e substitua o seu ficheiro Bicep pelo seu conteúdo.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Nos tutoriais anteriores, criou uma conta de armazenamento no Leste dos EUA, mas o seu grupo de recursos foi criado nos EUA Centrais. Para este tutorial, a sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para a localização, para que não seja necessário fornecer esse valor de parâmetro. Você deve fornecer um novo nome para a conta de armazenamento porque você está criando uma conta de armazenamento em um local diferente. Por exemplo, utilize **a loja2** como prefixo em vez de **armazenar1**.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Você vê que um recurso de conta de armazenamento foi implementado e tem a mesma localização que o grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou uma função ao definir o valor predefinido para um parâmetro. Nesta série tutorial, continuará a utilizar funções. No final da série, irá adicionar funções a todas as secções do ficheiro Bicep.

> [!div class="nextstepaction"]
> [Adicionar variáveis](bicep-tutorial-add-variables.md)
