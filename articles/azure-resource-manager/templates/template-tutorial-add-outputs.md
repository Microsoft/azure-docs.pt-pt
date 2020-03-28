---
title: Tutorial - adicione saídas ao modelo
description: Adicione saídas ao seu modelo de Gestor de Recursos Azure para simplificar a sintaxe.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5d8966c492bd77eaa4d18c8a8b9ac524b8864027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371750"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Tutorial: Adicione saídas ao seu modelo ARM

Neste tutorial, aprende a devolver um valor do seu modelo de Gestor de Recursos Azure (ARM). Utiliza-se saídas quando se precisa de um valor a partir de um recurso implantado. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre variáveis,](template-tutorial-add-variables.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Implementa uma conta de armazenamento, mas não devolve nenhuma informação sobre a conta de armazenamento. Você pode precisar capturar propriedades de um novo recurso para que estejam disponíveis mais tarde para referência.

## <a name="add-outputs"></a>Adicionar saídas

Pode utilizar saídas para devolver valores do modelo. Por exemplo, pode ser útil obter os pontos finais para a sua nova conta de armazenamento.

O exemplo seguinte realça a alteração no seu modelo para adicionar um valor de saída. Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Há alguns itens importantes a notar sobre o valor de saída que adicionou.

O tipo de valor devolvido está definido para **o objeto,** o que significa que devolve um objeto JSON.

Utiliza a função [de referência](template-functions-resource.md#reference) para obter o estado de funcionamento da conta de armazenamento. Para obter o estado de execução de um recurso, você passa no nome ou id de um recurso. Neste caso, utiliza a mesma variável que usou para criar o nome da conta de armazenamento.

Finalmente, devolve a propriedade **primaryEndpoints** da conta de armazenamento

## <a name="deploy-template"></a>Implementar o modelo

Está pronto para implementar o modelo e olhar para o valor devolvido.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Na saída para o comando de implantação, verá um objeto semelhante a:

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

## <a name="review-your-work"></a>Reveja o seu trabalho

Fizeste muito nos últimos seis tutoriais. Vamos rever o que fez. Criou um modelo com parâmetros fáceis de fornecer. O modelo é reutilizável em diferentes ambientes porque permite a personalização e cria dinamicamente os valores necessários. Também devolve informações sobre a conta de armazenamento que pode utilizar no seu script.

Agora, vamos olhar para o grupo de recursos e o histórico de implantação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. Dependendo dos passos que fez, deve ter pelo menos uma e talvez várias contas de armazenamento no grupo de recursos.
1. Você também deve ter várias implementações bem sucedidas listadas na história. Selecione o link.

   ![Selecione implementações](./media/template-tutorial-add-outputs/select-deployments.png)

1. Vê todos os seus destacamentos na história. Selecione a implantação chamada **addoutputs**.

   ![Mostrar histórico de implantação](./media/template-tutorial-add-outputs/show-history.png)

1. Pode rever as inputs.

   ![Mostrar inputs](./media/template-tutorial-add-outputs/show-inputs.png)

1. Pode rever as saídas.

   ![Mostrar saídas](./media/template-tutorial-add-outputs/show-outputs.png)

1. Pode rever o modelo.

   ![Modelo de show](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você adicionou um valor de retorno ao modelo. No próximo tutorial, você vai aprender a exportar um modelo e usar partes desse modelo exportado no seu modelo.

> [!div class="nextstepaction"]
> [Use modelo exportado](template-tutorial-export-template.md)
