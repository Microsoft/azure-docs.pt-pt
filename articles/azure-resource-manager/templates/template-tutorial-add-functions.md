---
title: Tutorial - adicione funções de modelo
description: Adicione as funções do modelo ao seu modelo de Gestor de Recursos Azure (modelo ARM) para construir valores.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 67c88e98d966a21163aafefcad8363086d5b3bf4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931049"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Tutorial: Adicione funções de modelo ao seu modelo ARM

Neste tutorial, você aprende a adicionar [funções de modelo](template-functions.md) ao seu modelo de Gestor de Recursos Azure (modelo ARM). Utiliza-se funções para construir valores dinâmicos. Além destas funções de modelo fornecidas pelo sistema, também pode criar [funções definidas pelo utilizador](./template-user-defined-functions.md). Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre parâmetros,](template-tutorial-add-parameters.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

A localização da conta de armazenamento é codificada para os **EUA.** No entanto, poderá ser necessário implantar a conta de armazenamento noutras regiões. Está novamente a enfrentar um problema do seu modelo sem flexibilidade. Pode adicionar um parâmetro para a localização, mas seria ótimo se o seu valor padrão fizesse mais sentido do que apenas um valor codificado.

## <a name="use-function"></a>Função de utilização

Se completou o tutorial anterior nesta série, já utilizou uma função. Quando `"[parameters('storageName')]"` adicionado, utilizou a função [de parâmetros.](template-functions-deployment.md#parameters) Os suportes indicam que a sintaxe dentro dos suportes é uma [expressão do modelo](template-expressions.md). O Gestor de Recursos resolve a sintaxe em vez de a tratar como um valor literal.

As funções adicionam flexibilidade ao seu modelo obtendo valores dinamicamente durante a implementação. Neste tutorial, você usa uma função para obter a localização do grupo de recursos que você está usando para implantação.

O exemplo a seguir destaca as alterações para adicionar um parâmetro chamado **localização**.  O valor predefinido do parâmetro chama a função [grupo de recursos.](template-functions-resource.md#resourcegroup) Esta função devolve um objeto com informações sobre o grupo de recursos que está a ser utilizado para a implantação. Uma das propriedades do objeto é uma propriedade de localização. Quando utiliza o valor predefinido, a localização da conta de armazenamento tem a mesma localização que o grupo de recursos. Os recursos dentro de um grupo de recursos não têm que partilhar o mesmo local. Também pode fornecer uma localização diferente quando necessário.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Implementar o modelo

Nos tutoriais anteriores, criou uma conta de armazenamento no Leste dos EUA, mas o seu grupo de recursos foi criado nos EUA Centrais. Para este tutorial, a sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para a localização, para que não seja necessário fornecer esse valor de parâmetro. Você deve fornecer um novo nome para a conta de armazenamento porque você está criando uma conta de armazenamento em um local diferente. Por exemplo, utilize **a loja2** como prefixo em vez de **armazenar1**.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Se a implementação falhar, utilize o interruptor **verboso** para obter informações sobre os recursos que estão a ser criados. Use o **interruptor de depuração** para obter mais informações para depurar.

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

Neste tutorial, utilizou uma função ao definir o valor predefinido para um parâmetro. Nesta série tutorial, continuará a utilizar funções. No final da série, irá adicionar funções a todas as secções do modelo.

> [!div class="nextstepaction"]
> [Adicionar variáveis](template-tutorial-add-variables.md)
