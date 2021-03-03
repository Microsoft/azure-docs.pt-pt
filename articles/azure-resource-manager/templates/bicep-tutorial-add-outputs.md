---
title: Tutorial - adicione saídas ao ficheiro Bicep do Azure Resource Manager
description: Adicione saídas ao seu ficheiro Bicep para simplificar a sintaxe.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 279417f22ded89db21abddad7a91a4cce520c6bd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748207"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar saídas ao ficheiro Bicep do Azure Resource Manager

Neste tutorial, aprende-se a devolver um valor da sua implantação. Utiliza-se saídas quando se precisa de um valor a partir de um recurso implantado. Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre variáveis,](bicep-tutorial-add-variables.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Implementa uma conta de armazenamento, mas não devolve nenhuma informação sobre a conta de armazenamento. Pode precisar de capturar propriedades a partir de um novo recurso para que estejam disponíveis mais tarde para referência.

## <a name="add-outputs"></a>Adicionar saídas

Pode utilizar saídas para devolver valores da implementação. Por exemplo, pode ser útil obter os pontos finais da sua nova conta de armazenamento.

O exemplo a seguir destaca a alteração no seu ficheiro Bicep para adicionar um valor de saída. Copie todo o ficheiro e substitua o seu ficheiro Bicep pelo seu conteúdo.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Existem alguns itens importantes a notar sobre o valor de saída que adicionou.

O tipo de valor devolvido é definido para `object` , o que significa que devolve um objeto de modelo.

Para obter a `primaryEndpoints` propriedade da conta de armazenamento, você usa o nome simbólico da conta de armazenamento.

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Está pronto para implantar o ficheiro Bicep e ver o valor devolvido.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Na saída para o comando de implantação, verá um objeto semelhante ao seguinte exemplo apenas se a saída estiver no formato JSON:

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

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="review-your-work"></a>Reveja o seu trabalho

Fizeste muito nos últimos seis tutoriais. Vamos rever o que fez. Criou um ficheiro Bicep com parâmetros fáceis de fornecer. O ficheiro Bicep é reutilizável em diferentes ambientes porque permite personalizar e cria valores necessários. Também devolve informações sobre a conta de armazenamento que pode utilizar no seu script.

Agora, vamos olhar para o grupo de recursos e o histórico de implantação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Dependendo dos passos que fez, deve ter pelo menos uma e talvez várias contas de armazenamento no grupo de recursos.
1. Também deverá ter vários destacamentos bem sucedidos listados na história. Selecione o link.

   ![Selecione implementações](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Vê todos os seus destacamentos na história. Selecione os **addoutouts de implantação chamados**.

   ![Mostrar histórico de implantação](./media/bicep-tutorial-add-outputs/show-history.png)

1. Pode rever as entradas.

   ![Mostrar entradas](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Pode rever as saídas.

   ![Mostrar saídas](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Pode rever o modelo JSON.

   ![Modelo de show](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou um valor de retorno ao ficheiro Bicep. No próximo tutorial, você vai aprender a exportar um modelo JSON e usar partes desse modelo exportado no seu arquivo Bicep.

> [!div class="nextstepaction"]
> [Use o modelo exportado](bicep-tutorial-export-template.md)
