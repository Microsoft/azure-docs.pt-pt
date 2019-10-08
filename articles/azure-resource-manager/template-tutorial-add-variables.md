---
title: Tutorial-Adicionar variável ao modelo de Azure Resource Manager
description: Adicione variáveis ao seu modelo de Azure Resource Manager para simplificar a sintaxe.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fb577c0c0f00da3735da98fc2e94f3610c9bd80d
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001462"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Tutorial: Adicionar variáveis ao modelo do Resource Manager

Neste tutorial, você aprenderá a adicionar uma variável ao seu modelo. As variáveis simplificam seus modelos permitindo que você grave uma expressão uma vez e reutilize-a em todo o modelo. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre as funções](template-tutorial-add-functions.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examine seu modelo

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

O parâmetro para o nome da conta de armazenamento é difícil de usar porque você precisa fornecer um nome exclusivo. Se você tiver concluído os tutoriais anteriores nesta série, provavelmente estará cansado de adivinhar um nome exclusivo. Você resolve esse problema adicionando uma variável que constrói um nome exclusivo para a conta de armazenamento.

## <a name="use-variable"></a>Usar variável

O exemplo a seguir realça as alterações para adicionar uma variável ao modelo que cria um nome de conta de armazenamento exclusivo. Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Observe que ele inclui uma variável chamada **uniqueStorageName**. Essa variável usa quatro funções para construir um valor de cadeia de caracteres.

Você já está familiarizado com a função [Parameters](resource-group-template-functions-deployment.md#parameters) , portanto, não a examinaremos.

Você também está familiarizado com a função [resourcegroup](resource-group-template-functions-resource.md#resourcegroup) . Nesse caso, você obtém a propriedade **ID** em vez da propriedade **Location** , conforme mostrado no tutorial anterior. A propriedade **ID** retorna o identificador completo do grupo de recursos, incluindo a ID da assinatura e o nome do grupo de recursos.

A função [uniquestring](resource-group-template-functions-string.md#uniquestring) cria um valor de hash de 13 caracteres. O valor retornado é determinado pelos parâmetros que você passa. Para este tutorial, você usa a ID do grupo de recursos como a entrada para o valor de hash. Isso significa que você pode implantar esse modelo em grupos de recursos diferentes e obter um valor de cadeia de caracteres exclusivo diferente. No entanto, você obterá o mesmo valor se implantar no mesmo grupo de recursos.

A função [concat](resource-group-template-functions-string.md#concat) usa valores e os combina. Para essa variável, ela usa a cadeia de caracteres do parâmetro e a cadeia de caracteres da função uniquestring e as combina em uma cadeia de caracteres.

O parâmetro **storagePrefix** permite que você passe um prefixo que ajuda a identificar contas de armazenamento. Você pode criar sua própria convenção de nomenclatura que facilite a identificação das contas de armazenamento após a implantação de uma longa lista de recursos.

Por fim, observe que o nome de armazenamento agora está definido como a variável em vez de um parâmetro.

## <a name="deploy-the-template"></a>Implementar o modelo

Vamos implantar o modelo. A implantação desse modelo é mais fácil do que os modelos anteriores, pois você fornece apenas o prefixo para o nome de armazenamento.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-the-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. Você verá que um recurso de conta de armazenamento foi implantado. O nome da conta de armazenamento é **armazenamento** mais uma cadeia de caracteres aleatórios.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você adicionou uma variável que cria um nome exclusivo para uma conta de armazenamento. No próximo tutorial, você retorna um valor da conta de armazenamento implantada.

> [!div class="nextstepaction"]
> [Adicionar saídas](template-tutorial-add-outputs.md)
