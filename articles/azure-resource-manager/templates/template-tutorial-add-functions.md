---
title: Tutorial - adicionar funções de modelo
description: Adicione funções de modelo ao seu modelo de Gestor de Recursos Azure para construir valores.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6bb95d5a8022c6ac94dd8b7c777c65ff5e20617a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369918"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Tutorial: Adicionar funções de modelo ao seu modelo ARM

Neste tutorial, aprende a adicionar [funções](template-functions.md) de modelo ao seu modelo De gestor de recursos (ARM) Azure. Usa funções para construir valores dinâmicos. Além destas funções de modelo fornecidas pelo sistema, também pode criar [funções definidas pelo utilizador.](./template-user-defined-functions.md) Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre parâmetros,](template-tutorial-add-parameters.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

A localização da conta de armazenamento está codificada para **os EUA Orientais.** No entanto, poderá ter de implementar a conta de armazenamento para outras regiões. Está novamente a enfrentar um problema do seu modelo sem flexibilidade. Poderia adicionar um parâmetro para a localização, mas seria ótimo se o seu valor padrão fizesse mais sentido do que apenas um valor codificado.

## <a name="use-function"></a>Função de utilização

Se já completou o tutorial anterior nesta série, já usou uma função. Quando adicionou **"[parâmetros('storageName']",** utilizou a função [de parâmetros.](template-functions-deployment.md#parameters) Os suportes indicam que a sintaxe dentro dos suportes é uma [expressão de modelo](template-expressions.md). O Gestor de Recursos resolve a sintaxe em vez de a tratar como um valor literal.

As funções adicionam flexibilidade ao seu modelo obtendo dinamicamente valores durante a implementação. Neste tutorial, você usa uma função para obter a localização do grupo de recursos que você está usando para implementação.

O exemplo seguinte realça as alterações para adicionar um parâmetro chamado **localização**.  O valor padrão do parâmetro chama a função [de recursoGroup.](template-functions-resource.md#resourcegroup) Esta função devolve um objeto com informações sobre o grupo de recursos que está a ser utilizado para a implantação. Uma das propriedades do objeto é uma propriedade de localização. Quando utiliza o valor predefinido, a localização da conta de armazenamento tem a mesma localização que o grupo de recursos. Os recursos dentro de um grupo de recursos não têm de partilhar o mesmo local. Também pode fornecer um local diferente quando necessário.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Implementar o modelo

Nos tutoriais anteriores, criou uma conta de armazenamento no Leste dos EUA, mas o seu grupo de recursos foi criado nos EUA Centrais. Para este tutorial, a sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para a localização, para que não precise fornecer esse valor de parâmetro. Você deve fornecer um novo nome para a conta de armazenamento porque você está criando uma conta de armazenamento em um local diferente. Por exemplo, utilize a **loja2** como prefixo em vez de **armazenar1**.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. Você vê que um recurso de conta de armazenamento foi implementado e tem a mesma localização que o grupo de recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou uma função ao definir o valor padrão para um parâmetro. Nesta série tutorial, continuará a usar funções. No final da série, irá adicionar funções a todas as secções do modelo.

> [!div class="nextstepaction"]
> [Adicionar variáveis](template-tutorial-add-variables.md)
