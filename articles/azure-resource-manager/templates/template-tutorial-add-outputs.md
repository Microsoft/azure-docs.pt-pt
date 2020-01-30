---
title: Tutorial – adicionar saídas ao modelo
description: Adicione saídas ao seu modelo de Azure Resource Manager para simplificar a sintaxe.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 381f9f54a95b6d457aa65c7e8ef6abe49fe9eeea
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765736"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Tutorial: Adicionar saídas ao seu modelo do Resource Manager

Neste tutorial, você aprenderá a retornar um valor de seu modelo. Você usa saídas quando precisa de um valor de um recurso implantado. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre variáveis](template-tutorial-add-variables.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Ele implanta uma conta de armazenamento, mas não retorna nenhuma informação sobre a conta de armazenamento. Talvez seja necessário capturar as propriedades de um novo recurso para que elas estejam disponíveis posteriormente para referência.

## <a name="add-outputs"></a>Adicionar saídas

Você pode usar saídas para retornar valores do modelo. Por exemplo, pode ser útil obter os pontos de extremidade para sua nova conta de armazenamento.

O exemplo a seguir realça a alteração em seu modelo para adicionar um valor de saída. Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Há alguns itens importantes a serem observados sobre o valor de saída adicionado.

O tipo de valor retornado é definido como **Object**, o que significa que ele retorna um objeto JSON.

Ele usa a função de [referência](template-functions-resource.md#reference) para obter o estado de tempo de execução da conta de armazenamento. Para obter o estado de tempo de execução de um recurso, você passa o nome ou a ID de um recurso. Nesse caso, você usa a mesma variável usada para criar o nome da conta de armazenamento.

Por fim, ele retorna a propriedade **primaryEndpoints** da conta de armazenamento

## <a name="deploy-template"></a>Implementar o modelo

Você está pronto para implantar o modelo e examinar o valor retornado.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Na saída do comando de implantação, você verá um objeto semelhante a:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>Examine seu trabalho

Você fez muito nos últimos seis tutoriais. Vamos reservar um momento para examinar o que você fez. Você criou um modelo com parâmetros que são fáceis de fornecer. O modelo é reutilizável em ambientes diferentes porque permite a personalização e cria dinamicamente os valores necessários. Ele também retorna informações sobre a conta de armazenamento que você pode usar em seu script.

Agora, vamos examinar o grupo de recursos e o histórico de implantação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. Dependendo das etapas que você fez, você deve ter pelo menos uma e, talvez, várias contas de armazenamento no grupo de recursos.
1. Você também deve ter várias implantações bem-sucedidas listadas no histórico. Selecione esse link.

   ![Selecionar implantações](./media/template-tutorial-add-outputs/select-deployments.png)

1. Você vê todas as suas implantações no histórico. Selecione a implantação chamada **addoutputs**.

   ![Mostrar histórico de implantação](./media/template-tutorial-add-outputs/show-history.png)

1. Você pode examinar as entradas.

   ![Mostrar entradas](./media/template-tutorial-add-outputs/show-inputs.png)

1. Você pode examinar as saídas.

   ![Mostrar saídas](./media/template-tutorial-add-outputs/show-outputs.png)

1. Você pode examinar o modelo.

   ![Mostrar modelo](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você adicionou um valor de retorno ao modelo. No próximo tutorial, você aprenderá a exportar um modelo e a usar partes desse modelo exportado em seu modelo.

> [!div class="nextstepaction"]
> [Usar modelo exportado](template-tutorial-export-template.md)
