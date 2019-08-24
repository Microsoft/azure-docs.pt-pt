---
title: Eliminar grupo de recursos e recursos - Azure Resource Manager
description: Descreve como excluir recursos e grupos de recursos. Ele descreve como o Azure Resource Manager ordena a exclusão de recursos quando uma exclusão de um grupo de recursos. Ele descreve os códigos de resposta e a forma como o Resource Manager processa-las para determinar se a eliminação foi concluída com êxito.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 75cdeb88a68dece59d6b037592f7212fa895e821
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991706"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager o grupo de recursos e a exclusão de recursos

Este artigo mostra como excluir recursos e grupos de recursos. Ele descreve como o Azure Resource Manager ordena a exclusão de recursos quando você exclui um grupo de recursos.

## <a name="delete-resource-group"></a>Eliminar grupo de recursos

Use um dos métodos a seguir para excluir o grupo de recursos.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource-group-name>
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name <resource-group-name>
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No [portal](https://portal.azure.com), selecione o grupo de recursos que você deseja excluir.

1. Selecione **Eliminar grupo de recursos**.

   ![Eliminar grupo de recursos](./media/resource-group-delete/delete-group.png)

1. Para confirmar a exclusão, digite o nome do grupo de recursos

---

## <a name="delete-resource"></a>Eliminar recurso

Use um dos métodos a seguir para excluir um recurso.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No [portal](https://portal.azure.com), selecione o recurso que você deseja excluir.

1. Selecione **Eliminar**. A captura de tela a seguir mostra as opções de gerenciamento para uma máquina virtual.

   ![Eliminar recurso](./media/resource-group-delete/delete-resource.png)

1. Quando lhe for perguntado, confirme a eliminação.

---

## <a name="how-order-of-deletion-is-determined"></a>Como a ordem de exclusão é determinada

Quando elimina um grupo de recursos, o Resource Manager determina a ordem para eliminar recursos. Ele usa o seguinte ordem:

1. Todos os recursos subordinados (aninhados) são eliminados.

2. Recursos geridos por outros recursos são eliminados, em seguida. Um recurso pode ter o `managedBy` propriedade definida para indicar que um recurso diferente o gere. Quando essa propriedade é definida, o recurso que gere o outro recurso é eliminado antes os outros recursos.

3. Os recursos restantes são eliminados após as duas categorias anteriores.

Depois da ordem é determinada, o Resource Manager emite uma operação de eliminação para cada recurso. Ele aguarda todas as dependências concluir antes de continuar.

Para operações síncronas, são os códigos de resposta com êxito esperado:

* 200
* 204
* 404

Para operações assíncronas, a resposta bem-sucedida esperada é 202. Gestor de recursos controla o cabeçalho de localização ou o cabeçalho de operação do azure-async para determinar o estado da operação assíncrona delete.
  
### <a name="errors"></a>Erros

Quando uma operação de eliminação retorna um erro, o Resource Manager tentará novamente com a chamada de eliminação. As repetições ocorrem para 5xx, 429 e códigos de estado 408. Por predefinição, o período de tempo entre tentativas é de 15 minutos.

## <a name="after-deletion"></a>Após a eliminação

Gestor de recursos emite uma chamada GET para cada recurso que ele estava a tentar eliminar. A resposta a esta chamada GET deve ser 404. Quando o Resource Manager obtém um 404, considera a eliminação para serem concluídos com êxito. Gestor de recursos remove o recurso a partir da respetiva cache.

No entanto, se a chamada GET do recurso retorna um 200 ou 201, o Resource Manager recria o recurso.

### <a name="errors"></a>Erros

Se a operação de obtenção de devolver um erro, o Resource Manager repete GET para o seguinte código de erro:

* Menos de 100
* 408
* 429
* Superior a 500

Para outros códigos de erro, o Resource Manager não consegue a eliminação do recurso.

## <a name="next-steps"></a>Passos Seguintes

* Para compreender os conceitos do Resource Manager, veja [descrição geral do Azure Resource Manager](resource-group-overview.md).
* Para obter comandos eliminação, veja [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [CLI do Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete), e [REST API](/rest/api/resources/resourcegroups/delete).
