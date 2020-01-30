---
title: Tutorial – adicionar parâmetros ao modelo
description: Adicione parâmetros ao seu modelo de Azure Resource Manager para torná-lo reutilizável.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89101a96f4fc228e2d5c45d67e10b52ac5d8aa11
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773210"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Tutorial: adicionar parâmetros ao modelo do Resource Manager

No [tutorial anterior](template-tutorial-add-resource.md), você aprendeu como adicionar uma conta de armazenamento ao modelo e implantá-la. Neste tutorial, você aprenderá a melhorar o modelo adicionando parâmetros. Este tutorial leva cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre os recursos](template-tutorial-add-resource.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Talvez você tenha notado que há um problema com esse modelo. O nome da conta de armazenamento é embutido em código. Você só pode usar esse modelo para implantar a mesma conta de armazenamento a cada vez. Para implantar uma conta de armazenamento com um nome diferente, você precisaria criar um novo modelo, que obviamente não é uma maneira prática de automatizar suas implantações.

## <a name="make-template-reusable"></a>Tornar o modelo reutilizável

Para tornar seu modelo reutilizável, vamos adicionar um parâmetro que você pode usar para passar um nome de conta de armazenamento. O JSON realçado no exemplo a seguir mostra o que mudou em seu modelo. O parâmetro **storagename** é identificado como uma cadeia de caracteres. O comprimento máximo é definido como 24 caracteres para evitar qualquer nome muito longo.

Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Implementar o modelo

Vamos implantar o modelo. O exemplo a seguir implanta o modelo com CLI do Azure ou PowerShell. Observe que você fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome usado no tutorial anterior.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Entender as atualizações de recursos

Na seção anterior, você implantou uma conta de armazenamento com o mesmo nome que você criou anteriormente. Você pode estar se perguntando como o recurso é afetado pela reimplantação.

Se o recurso já existir e nenhuma alteração for detectada nas propriedades, nenhuma ação será executada. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, ele será criado.

Essa maneira de lidar com atualizações significa que seu modelo pode incluir todos os recursos necessários para uma solução do Azure. Você pode reimplantar com segurança o modelo e saber se os recursos são alterados ou criados somente quando necessário. Por exemplo, se você tiver adicionado arquivos à sua conta de armazenamento, poderá reimplantar a conta de armazenamento sem perder esses arquivos.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem-lhe personalizar a implementação, ao fornecer valores que são adaptados para um determinado ambiente. Por exemplo, você pode passar valores diferentes com base em se está implantando em um ambiente para desenvolvimento, teste e produção.

O modelo anterior sempre implementou uma conta de armazenamento Standard_LRS. Talvez você queira a flexibilidade para implantar SKUs diferentes dependendo do ambiente. O exemplo a seguir mostra as alterações para adicionar um parâmetro para a SKU. Copie todo o arquivo e cole-o em seu modelo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

O parâmetro **storageSKU** tem um valor padrão. Esse valor é usado quando um valor não é especificado durante a implantação. Ele também tem uma lista de valores permitidos. Esses valores correspondem aos valores necessários para criar uma conta de armazenamento. Você não quer que os usuários do seu modelo transmitam SKUs que não funcionam.

## <a name="redeploy-template"></a>Reimplementar o modelo

Você está pronto para implantar novamente. Como o SKU padrão está definido para **Standard_LRS,** você não precisa fornecer um valor para esse parâmetro.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Para ver a flexibilidade do modelo, vamos implantá-lo novamente. Desta vez, o parâmetro SKU **Standard_GRS**. Você pode passar um novo nome para criar uma conta de armazenamento diferente ou usar o mesmo nome para atualizar sua conta de armazenamento existente. Ambas as opções funcionam.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Por fim, vamos executar mais um teste e ver o que acontece quando você passa uma SKU que não é um dos valores permitidos. Nesse caso, testamos o cenário em que um usuário do modelo pensa que o **básico** é uma das SKUs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

O comando falha imediatamente com uma mensagem de erro que indica quais valores são permitidos. O Gerenciador de recursos identifica o erro antes de iniciar a implantação.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Você melhorou o modelo criado no [primeiro tutorial](template-tutorial-create-first-template.md) adicionando parâmetros. No próximo tutorial, você aprenderá sobre as funções de modelo.

> [!div class="nextstepaction"]
> [Adicionar funções de modelo](template-tutorial-add-functions.md)