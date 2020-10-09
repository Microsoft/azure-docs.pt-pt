---
title: Tutorial - adicione parâmetros ao modelo
description: Adicione parâmetros ao seu modelo de Gestor de Recursos Azure para torná-lo reutilizável.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9e860941c3d782498c12afa12f285f44e7e68f37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069446"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Tutorial: Adicione parâmetros ao seu modelo ARM

No [tutorial anterior,](template-tutorial-add-resource.md)aprendeu a adicionar uma conta de armazenamento ao modelo e implantá-la. Neste tutorial, aprende-se a melhorar o modelo Azure Resource Manager (ARM) adicionando parâmetros. Este tutorial leva cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre recursos,](template-tutorial-add-resource.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Você pode ter reparado que há um problema com este modelo. O nome da conta de armazenamento está hard-coded. Só pode utilizar este modelo para implementar sempre a mesma conta de armazenamento. Para implementar uma conta de armazenamento com um nome diferente, você teria que criar um novo modelo, o que obviamente não é uma forma prática de automatizar as suas implementações.

## <a name="make-template-reusable"></a>Tornar o modelo reutilizável

Para tornar o seu modelo reutilizável, vamos adicionar um parâmetro que pode usar para passar num nome de conta de armazenamento. O JSON destacado no exemplo a seguir mostra o que mudou no seu modelo. O **parâmetro de armazenamentoName** é identificado como uma corda. O comprimento máximo é definido para 24 caracteres para evitar nomes demasiado longos.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Implementar o modelo

Vamos implementar o modelo. O exemplo a seguir implanta o modelo com Azure CLI ou PowerShell. Note que fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome que usou no tutorial anterior.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender atualizações de recursos

Na secção anterior, implementou uma conta de armazenamento com o mesmo nome que tinha criado anteriormente. Podem estar a perguntar-se como é que o recurso é afetado pela reafectação.

Se o recurso já existir e não for detetada nenhuma alteração nas propriedades, não será realizada nenhuma ação. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, será criado.

Esta forma de lidar com atualizações significa que o seu modelo pode incluir todos os recursos necessários para uma solução Azure. Pode recolocar o modelo com segurança e saber que os recursos são alterados ou criados apenas quando necessário. Por exemplo, se tiver adicionado ficheiros à sua conta de armazenamento, pode recolocar a conta de armazenamento sem perder esses ficheiros.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem-lhe personalizar a implementação, ao fornecer valores que são adaptados para um determinado ambiente. Por exemplo, pode passar valores diferentes com base no facto de estar a implantar-se num ambiente para desenvolvimento, teste e produção.

O modelo anterior sempre implementou uma conta de armazenamento Standard_LRS. Pode querer a flexibilidade para implantar diferentes SKUs dependendo do ambiente. O exemplo a seguir mostra as alterações para adicionar um parâmetro para o SKU. Copie todo o ficheiro e cole sobre o seu modelo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

O parâmetro **storageSKU** tem um valor predefinido. Este valor é utilizado quando um valor não é especificado durante a implantação. Tem também uma lista de valores permitidos. Estes valores correspondem aos valores necessários para criar uma conta de armazenamento. Não quer que os utilizadores do seu modelo passem em SKUs que não funcionam.

## <a name="redeploy-template"></a>Reimplementar o modelo

Está pronto para partir de novo. Como o SKU predefinido está definido para **Standard_LRS,** não precisa de fornecer um valor para esse parâmetro.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Se a implementação falhar, utilize o interruptor **verboso** para obter informações sobre os recursos que estão a ser criados. Use o **interruptor de depuração** para obter mais informações para depurar.

Para ver a flexibilidade do seu modelo, vamos implementar novamente. Desta vez, de definiu o parâmetro SKU para **Standard_GRS**. Pode passar um novo nome para criar uma conta de armazenamento diferente, ou usar o mesmo nome para atualizar a sua conta de armazenamento existente. Ambas as opções funcionam.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Finalmente, vamos fazer mais um teste e ver o que acontece quando se passa num SKU que não é um dos valores permitidos. Neste caso, testamos o cenário em que um utilizador do seu modelo acha básico **é** um dos SKUs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Melhorou o modelo criado no [primeiro tutorial](template-tutorial-create-first-template.md) adicionando parâmetros. No próximo tutorial, você vai aprender sobre as funções do modelo.

> [!div class="nextstepaction"]
> [Adicionar funções de modelo](template-tutorial-add-functions.md)
