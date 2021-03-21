---
title: Tutorial - adicione parâmetros ao ficheiro Bicep do Azure Resource Manager
description: Adicione parâmetros ao seu ficheiro Bicep para torná-lo reutilizável.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632786"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar parâmetros ao ficheiro Bicep do Gestor de Recursos Azure

No [tutorial anterior,](bicep-tutorial-create-first-bicep.md)aprendeu a implantar uma conta de armazenamento. Neste tutorial, aprende-se a melhorar o ficheiro Bicep adicionando parâmetros. Este tutorial leva cerca de **14 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [primeiro ficheiro Bicep,](bicep-tutorial-create-first-bicep.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu Bicep parece:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Deve ter reparado que há um problema com este ficheiro Bicep. O nome da conta de armazenamento está hard-coded. Só pode utilizar este ficheiro Bicep para implantar sempre a mesma conta de armazenamento. Para implantar uma conta de armazenamento com um nome diferente, teria de criar um novo ficheiro Bicep, o que obviamente não é uma forma prática de automatizar as suas implementações.

## <a name="make-bicep-file-reusable"></a>Tornar o ficheiro Bicep reutilizável

Para tornar o seu ficheiro Bicep reutilizável, vamos adicionar um parâmetro que pode usar para passar num nome de conta de armazenamento. O seguinte ficheiro Bicep mostra o que mudou no seu ficheiro. O `storageName` parâmetro é identificado como uma corda. O comprimento máximo é definido para 24 caracteres para evitar nomes que sejam demasiado longos.

Copie todo o ficheiro e substitua-o pelos seguintes conteúdos.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Note que os parâmetros podem ser referenciados diretamente utilizando os seus nomes em Bicep, em comparação com a necessidade de [parâmetros ('storageName')] no modelo ARM JSON.

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Vamos implantar o ficheiro Bicep. O exemplo a seguir implanta o ficheiro Bicep com Azure CLI ou PowerShell. Note que fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome que usou no tutorial anterior.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender atualizações de recursos

Na secção anterior, implementou uma conta de armazenamento com o mesmo nome que tinha criado anteriormente. Podem estar a perguntar-se como é que o recurso é afetado pela reafectação.

Se o recurso já existir e não for detetada nenhuma alteração nas propriedades, não será realizada nenhuma ação. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, será criado.

Esta forma de lidar com atualizações significa que o seu ficheiro Bicep pode incluir todos os recursos necessários para uma solução Azure. Pode recolocar com segurança o ficheiro Bicep e saber que os recursos são alterados ou criados apenas quando necessário. Por exemplo, se tiver adicionado ficheiros à sua conta de armazenamento, pode recolocar a conta de armazenamento sem perder esses ficheiros.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem-lhe personalizar a implementação, ao fornecer valores que são adaptados para um determinado ambiente. Por exemplo, pode passar valores diferentes com base no facto de estar a implantar-se num ambiente para desenvolvimento, teste e produção.

O ficheiro Bicep anterior sempre implementou uma conta de armazenamento **Standard_LRS.** Pode querer a flexibilidade para implantar diferentes SKUs dependendo do ambiente. O exemplo a seguir mostra as alterações para adicionar um parâmetro para o SKU. Copie todo o ficheiro e cole-o sobre o seu ficheiro Bicep.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

O `storageSKU` parâmetro tem um valor padrão. Este valor é utilizado quando um valor não é especificado durante a implantação. Tem também uma lista de valores permitidos. Estes valores correspondem aos valores necessários para criar uma conta de armazenamento. Não quer que os utilizadores do seu ficheiro Bicep passem em SKUs que não funcionam.

## <a name="redeploy-bicep-file"></a>Reposicionar ficheiro Bicep

Está pronto para partir de novo. Como o SKU predefinido está definido para **Standard_LRS,** não precisa de fornecer um valor para esse parâmetro.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

Para ver a flexibilidade do seu ficheiro Bicep, vamos implementar novamente. Desta vez, de definiu o parâmetro SKU para **Standard_GRS**. Pode passar um novo nome para criar uma conta de armazenamento diferente, ou usar o mesmo nome para atualizar a sua conta de armazenamento existente. Ambas as opções funcionam.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Finalmente, vamos fazer mais um teste e ver o que acontece quando se passa num SKU que não é um dos valores permitidos. Neste caso, testamos o cenário em que um utilizador do seu ficheiro Bicep acha que **o básico** é um dos SKUs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

O comando falha imediatamente com uma mensagem de erro que indica quais os valores permitidos. O Gestor de Recursos identifica o erro antes do início da implantação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Melhorou o ficheiro Bicep criado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md) adicionando parâmetros. No próximo tutorial, aprenderá sobre as funções Bicep.

> [!div class="nextstepaction"]
> [Adicionar funções](bicep-tutorial-add-functions.md)
