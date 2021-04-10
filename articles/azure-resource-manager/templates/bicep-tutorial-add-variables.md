---
title: Tutorial - adicione variável ao ficheiro Bicep do Azure Resource Manager
description: Adicione variáveis ao seu ficheiro Bicep para simplificar a sintaxe.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632480"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar variáveis ao ficheiro Bicep do Gestor de Recursos Azure

Neste tutorial, aprende-se a adicionar uma variável ao seu ficheiro Bicep. As variáveis simplificam os seus ficheiros Bicep, permitindo-lhe escrever uma expressão uma vez e reutilizá-la em todo o ficheiro Bicep. Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre funções,](bicep-tutorial-add-functions.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

O parâmetro para o nome da conta de armazenamento é difícil de usar porque você tem que fornecer um nome único. Se completou os tutoriais anteriores desta série, provavelmente está cansado de adivinhar um nome único. Resolve-se este problema adicionando uma variável que constrói um nome único para a conta de armazenamento.

## <a name="use-variable"></a>Utilizar variável

O exemplo a seguir mostra as alterações para adicionar uma variável ao seu ficheiro Bicep que cria um nome de conta de armazenamento único. Copie todo o ficheiro e substitua o seu ficheiro Bicep pelo seu conteúdo.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Note que inclui uma variável chamada `uniqueStorageName` . Esta variável usa três funções para construir um valor de corda.

Está familiarizado com a função [grupo de recursos.](template-functions-resource.md#resourcegroup) Neste caso, obtém-se a `id` propriedade em vez da `location` propriedade, como mostra o tutorial anterior. A `id` propriedade devolve o identificador completo do grupo de recursos, incluindo o ID de subscrição e o nome do grupo de recursos.

A função [unímos o adc de 13](template-functions-string.md#uniquestring) caracteres cria um valor de haxixe de 13 caracteres. O valor devolvido é determinado pelos parâmetros que se passam. Para este tutorial, utilize o ID do grupo de recursos como entrada para o valor do haxixe. Isto significa que pode implementar este ficheiro Bicep em diferentes grupos de recursos e obter um valor de cadeia único diferente. No entanto, obtém o mesmo valor se implementar para o mesmo grupo de recursos.

Bicep suporta uma sintaxe de interpolação de [cordas.](https://en.wikipedia.org/wiki/String_interpolation#) Para esta variável, retira a corda do parâmetro e da corda da `uniqueString` função, e combina-as numa corda.

O `storagePrefix` parâmetro permite-lhe passar num prefixo que o ajuda a identificar contas de armazenamento. Você pode criar a sua própria convenção de nomeação que facilita a identificação de contas de armazenamento após a implantação de uma longa lista de recursos.

Finalmente, note que o nome de armazenamento está agora definido para a variável em vez de um parâmetro.

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Vamos implantar o ficheiro Bicep. A implementação deste ficheiro Bicep é mais fácil do que os ficheiros Bicep anteriores, pois fornece apenas o prefixo para o nome de armazenamento.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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
> [Adicionar saídas](bicep-tutorial-add-outputs.md)
