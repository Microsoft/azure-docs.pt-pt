---
title: Tutorial - adicione variável ao modelo
description: Adicione variáveis ao seu modelo de Gestor de Recursos Azure para simplificar a sintaxe.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b1df86e5b593edec784de21e21a4399274d820bb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411692"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Tutorial: Adicione variáveis ao seu modelo ARM

Neste tutorial, aprende a adicionar uma variável ao seu modelo de Gestor de Recursos Azure (ARM). As variáveis simplificam os seus modelos, permitindo-lhe escrever uma expressão uma vez e reutilizá-la ao longo do modelo. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre funções,](template-tutorial-add-functions.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

O parâmetro para o nome da conta de armazenamento é difícil de usar porque você tem que fornecer um nome único. Se já completou os tutoriais anteriores desta série, provavelmente está cansado de adivinhar um nome único. Resolve-se este problema adicionando uma variável que constrói um nome único para a conta de armazenamento.

## <a name="use-variable"></a>Usar variável

O exemplo seguinte realça as alterações para adicionar uma variável ao seu modelo que cria um nome de conta de armazenamento único. Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Note que inclui uma variável chamada **UniqueStorageName**. Esta variável usa quatro funções para construir um valor de cadeia.

Já está familiarizado com a função de [parâmetros,](template-functions-deployment.md#parameters) por isso não vamos examiná-la.

Também está familiarizado com a função [de recursoGroup.](template-functions-resource.md#resourcegroup) Neste caso, obtém-se a propriedade **id** em vez da propriedade de **localização,** como mostra o tutorial anterior. A propriedade **id** devolve o identificador completo do grupo de recursos, incluindo o ID de subscrição e o nome do grupo de recursos.

A função [String única](template-functions-string.md#uniquestring) cria um valor hash de 13 caracteres. O valor devolvido é determinado pelos parâmetros que passa. Para este tutorial, você usa o ID do grupo de recursos como a entrada para o valor do haxixe. Isso significa que você poderia implementar este modelo para diferentes grupos de recursos e obter um valor de cadeia único diferente. No entanto, obtém o mesmo valor se se deslocar para o mesmo grupo de recursos.

A função [concat](template-functions-string.md#concat) toma valores e combina-os. Para esta variável, retira a corda do parâmetro e a corda da função String única, e combina-as numa só corda.

O parâmetro **de prefixo de armazenamento** permite-lhe passar num prefixo que o ajuda a identificar contas de armazenamento. Pode criar a sua própria convenção de nomeação que facilita a identificação de contas de armazenamento após a implantação de uma longa lista de recursos.

Finalmente, note que o nome de armazenamento está agora definido para a variável em vez de um parâmetro.

## <a name="deploy-template"></a>Implementar o modelo

Vamos colocar o modelo. A implementação deste modelo é mais fácil do que os modelos anteriores porque fornece apenas o prefixo para o nome de armazenamento.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

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

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se a implantação falhar, utilize o interruptor **de depuração** com o comando de implantação para mostrar os registos de depuração.  Também pode utilizar o interruptor **verboso** para mostrar os registos completos de depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. Sabe que foi implementado um recurso de conta de armazenamento. O nome da conta de armazenamento é **loja** mais uma série de caracteres aleatórios.

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou uma variável que cria um nome único para uma conta de armazenamento. No próximo tutorial, devolve-se um valor da conta de armazenamento implantada.

> [!div class="nextstepaction"]
> [Adicionar saídas](template-tutorial-add-outputs.md)
