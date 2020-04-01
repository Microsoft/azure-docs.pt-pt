---
title: Tutorial - adicione parâmetros ao modelo
description: Adicione parâmetros ao seu modelo de Gestor de Recursos Azure para torná-lo reutilizável.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de7ec961672db2f3120e00f1a42b33f71e7ab092
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437821"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Tutorial: Adicione parâmetros ao seu modelo ARM

No [tutorial anterior,](template-tutorial-add-resource.md)aprendeu a adicionar uma conta de armazenamento ao modelo e implantá-la. Neste tutorial, aprende-se a melhorar o modelo do Gestor de Recursos Azure (ARM), adicionando parâmetros. Este tutorial leva cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre recursos,](template-tutorial-add-resource.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Deve ter reparado que há um problema com este modelo. O nome da conta de armazenamento é codificado. Só pode utilizar este modelo para implementar sempre a mesma conta de armazenamento. Para implementar uma conta de armazenamento com um nome diferente, você teria que criar um novo modelo, que obviamente não é uma forma prática de automatizar as suas implementações.

## <a name="make-template-reusable"></a>Tornar o modelo reutilizável

Para tornar o seu modelo reutilizável, vamos adicionar um parâmetro que pode usar para passar num nome de conta de armazenamento. O JSON destacado no exemplo seguinte mostra o que mudou no seu modelo. O parâmetro **de nome** de armazenamento é identificado como uma corda. O comprimento máximo é definido para 24 caracteres para evitar quaisquer nomes que sejam demasiado longos.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Implementar o modelo

Vamos colocar o modelo. O exemplo seguinte implementa o modelo com Azure CLI ou PowerShell. Note que fornece o nome da conta de armazenamento como um dos valores do comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome que usou no tutorial anterior.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender as atualizações de recursos

Na secção anterior, implementou uma conta de armazenamento com o mesmo nome que tinha criado anteriormente. Pode estar a perguntar-se como é que o recurso é afetado pela reafectação.

Se o recurso já existir e não for detetada qualquer alteração nas propriedades, não são tomadas quaisquer medidas. Se o recurso já existe e um imóvel mudou, o recurso é atualizado. Se o recurso não existe, é criado.

Esta forma de lidar com atualizações significa que o seu modelo pode incluir todos os recursos necessários para uma solução Azure. Pode recolocar o modelo com segurança e saber que os recursos são alterados ou criados apenas quando necessário. Por exemplo, se tiver adicionado ficheiros à sua conta de armazenamento, pode recolocar a conta de armazenamento sem perder esses ficheiros.

## <a name="customize-by-environment"></a>Personalize por meio ambiente

Os parâmetros permitem-lhe personalizar a implementação, ao fornecer valores que são adaptados para um determinado ambiente. Por exemplo, você pode passar diferentes valores com base em se você está implantando para um ambiente de desenvolvimento, teste e produção.

O modelo anterior sempre implementou uma conta de armazenamento Standard_LRS. Talvez queira a flexibilidade para implementar diferentes UsKUs dependendo do ambiente. O exemplo seguinte mostra as alterações para adicionar um parâmetro para SKU. Copie todo o ficheiro e cole sobre o seu modelo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

O parâmetro **storageSKU** tem um valor predefinido. Este valor é utilizado quando um valor não é especificado durante a implementação. Também tem uma lista de valores permitidos. Estes valores correspondem aos valores necessários para criar uma conta de armazenamento. Não quer que os utilizadores do seu modelo passem em SKUs que não funcionem.

## <a name="redeploy-template"></a>Reimplementar o modelo

Está pronto para partir de novo. Como o SKU padrão está definido para **Standard_LRS,** você não precisa fornecer um valor para esse parâmetro.

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
> Se a implantação falhar, utilize o interruptor **de depuração** com o comando de implantação para mostrar os registos de depuração.  Também pode utilizar o interruptor **verboso** para mostrar os registos completos de depuração.

Para ver a flexibilidade do seu modelo, vamos implantar novamente. Desta vez, o parâmetro SKU **Standard_GRS**. Pode passar um novo nome para criar uma conta de armazenamento diferente, ou usar o mesmo nome para atualizar a sua conta de armazenamento existente. Ambas as opções funcionam.

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

Finalmente, vamos fazer mais um teste e ver o que acontece quando se passa num SKU que não é um dos valores permitidos. Neste caso, testamos o cenário em que um utilizador do seu modelo acha **que o básico** é um dos SKUs.

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

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Melhorou o modelo criado no [primeiro tutorial](template-tutorial-create-first-template.md) adicionando parâmetros. No próximo tutorial, você aprenderá sobre funções de modelo.

> [!div class="nextstepaction"]
> [Adicionar funções de modelo](template-tutorial-add-functions.md)