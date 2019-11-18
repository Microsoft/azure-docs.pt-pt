---
title: Tutorial – adicionar funções de modelo
description: Adicione funções de modelo ao seu modelo de Azure Resource Manager para construir valores.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fe571c2a0088375feff8351f49a476669461b6aa
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150259"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Tutorial: adicionar funções de modelo ao seu modelo do Resource Manager

Neste tutorial, você aprenderá a adicionar [funções de modelo](resource-group-template-functions.md) ao seu modelo. Você usa funções para construir valores dinamicamente. Além dessas funções de modelo fornecidas pelo sistema, você também pode criar [funções definidas pelo usuário](./template-user-defined-functions.md). Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre os parâmetros](template-tutorial-add-parameters.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examine seu modelo

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

O local da conta de armazenamento é embutido no código para **leste dos EUA**. No entanto, talvez seja necessário implantar a conta de armazenamento em outras regiões. Você está voltado para um problema de seu modelo sem flexibilidade. Você pode adicionar um parâmetro para local, mas seria ótimo se seu valor padrão fazia mais sentido do que apenas um valor embutido em código.

## <a name="use-function"></a>Usar função

Se você tiver concluído o tutorial anterior nesta série, já usou uma função. Quando você adicionou **"[Parameters (' storagename ')]"** , usou a função [Parameters](resource-group-template-functions-deployment.md#parameters) . Os colchetes indicam que a sintaxe dentro dos colchetes é uma [expressão de modelo](template-expressions.md). O Gerenciador de recursos resolve a sintaxe em vez de tratá-la como um valor literal.

As funções adicionam flexibilidade ao modelo ao obter valores dinamicamente durante a implantação. Neste tutorial, você usa uma função para obter o local do grupo de recursos que você está usando para implantação.

O exemplo a seguir realça as alterações para adicionar um parâmetro chamado **Location**.  O valor padrão do parâmetro chama a função [resourcegroup](resource-group-template-functions-resource.md#resourcegroup) . Essa função retorna um objeto com informações sobre o grupo de recursos que está sendo usado para implantação. Uma das propriedades no objeto é uma propriedade Location. Quando você usa o valor padrão, o local da conta de armazenamento tem o mesmo local que o grupo de recursos. Os recursos dentro de um grupo de recursos não precisam compartilhar o mesmo local. Você também pode fornecer um local diferente quando necessário.

Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Implementar o modelo

Nos tutoriais anteriores, você criou uma conta de armazenamento no leste dos EUA, mas seu grupo de recursos foi criado no EUA Central. Para este tutorial, sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para local, para que você não precise fornecer esse valor de parâmetro. Você deve fornecer um novo nome para a conta de armazenamento porque está criando uma conta de armazenamento em um local diferente. Por exemplo, use **Store2** como o prefixo em vez de **Store1**.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-the-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. Você verá que um recurso de conta de armazenamento foi implantado e tem o mesmo local que o grupo de recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você usou uma função ao definir o valor padrão para um parâmetro. Nesta série de tutoriais, você continuará usando o functions. Ao final da série, você adicionará funções a cada seção do modelo.

> [!div class="nextstepaction"]
> [Adicionar variáveis](template-tutorial-add-variables.md)
