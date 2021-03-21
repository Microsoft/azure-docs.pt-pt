---
title: Tutorial - adicione variável ao modelo
description: Adicione variáveis ao seu modelo de Gestor de Recursos Azure (modelo ARM) para simplificar a sintaxe.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46ed1fc55a108bf80089d249abc58bc5d1a6479a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106959"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Tutorial: Adicione variáveis ao seu modelo ARM

Neste tutorial, você aprende a adicionar uma variável ao seu modelo de Gestor de Recursos Azure (modelo ARM). As variáveis simplificam os seus modelos, permitindo-lhe escrever uma expressão uma vez e reutilizá-la ao longo do modelo. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre funções,](template-tutorial-add-functions.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

O parâmetro para o nome da conta de armazenamento é difícil de usar porque você tem que fornecer um nome único. Se completou os tutoriais anteriores desta série, provavelmente está cansado de adivinhar um nome único. Resolve-se este problema adicionando uma variável que constrói um nome único para a conta de armazenamento.

## <a name="use-variable"></a>Utilizar variável

O exemplo a seguir destaca as alterações para adicionar uma variável ao seu modelo que cria um nome de conta de armazenamento único. Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Note que inclui uma variável chamada `uniqueStorageName` . Esta variável usa quatro funções para construir um valor de corda.

Já está familiarizado com a função dos [parâmetros,](template-functions-deployment.md#parameters) por isso não vamos examiná-la.

Também está familiarizado com a função [grupo de recursos.](template-functions-resource.md#resourcegroup) Neste caso, obtém-se a `id` propriedade em vez da `location` propriedade, como mostra o tutorial anterior. A `id` propriedade devolve o identificador completo do grupo de recursos, incluindo o ID de subscrição e o nome do grupo de recursos.

A função [unímos o adc de 13](template-functions-string.md#uniquestring) caracteres cria um valor de haxixe de 13 caracteres. O valor devolvido é determinado pelos parâmetros que se passam. Para este tutorial, utilize o ID do grupo de recursos como entrada para o valor do haxixe. Isto significa que você poderia implementar este modelo para diferentes grupos de recursos e obter um valor de cadeia único diferente. No entanto, obtém o mesmo valor se implementar para o mesmo grupo de recursos.

A função [concat](template-functions-string.md#concat) toma valores e combina-os. Para esta variável, retira a corda do parâmetro e da corda da `uniqueString` função, e combina-as numa corda.

O `storagePrefix` parâmetro permite-lhe passar num prefixo que o ajuda a identificar contas de armazenamento. Você pode criar a sua própria convenção de nomeação que facilita a identificação de contas de armazenamento após a implantação de uma longa lista de recursos.

Finalmente, note que o nome de armazenamento está agora definido para a variável em vez de um parâmetro.

## <a name="deploy-template"></a>Implementar o modelo

Vamos implementar o modelo. A implementação deste modelo é mais fácil do que os modelos anteriores porque fornece apenas o prefixo para o nome de armazenamento.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a `templateFile` variável para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Vê que foi implantado um recurso de conta de armazenamento. O nome da conta de armazenamento é **loja** mais uma série de caracteres aleatórios.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou uma variável que cria um nome único para uma conta de armazenamento. No tutorial seguinte, devolve-se um valor da conta de armazenamento implantada.

> [!div class="nextstepaction"]
> [Adicionar saídas](template-tutorial-add-outputs.md)
