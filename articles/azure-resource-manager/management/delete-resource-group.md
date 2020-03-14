---
title: Eliminar grupo de recursos e recursos
description: Descreve como eliminar grupos e recursos de recursos. Descreve como o Gestor de Recursos azure ordena a eliminação de recursos quando um grupo de recursos elimina. Ele descreve os códigos de resposta e a forma como o Resource Manager processa-las para determinar se a eliminação foi concluída com êxito.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274024"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Grupo de recursos do Gestor de Recursos azure e eliminação de recursos

Este artigo mostra como eliminar grupos e recursos de recursos. Descreve como o Gestor de Recursos azure ordena a eliminação de recursos quando elimina um grupo de recursos.

## <a name="how-order-of-deletion-is-determined"></a>Como a ordem de eliminação é determinada

Quando elimina um grupo de recursos, o Resource Manager determina a ordem para eliminar recursos. Ele usa o seguinte ordem:

1. Todos os recursos subordinados (aninhados) são eliminados.

2. Recursos geridos por outros recursos são eliminados, em seguida. Um recurso pode ter o `managedBy` propriedade definida para indicar que um recurso diferente gere-o. Quando essa propriedade é definida, o recurso que gere o outro recurso é eliminado antes os outros recursos.

3. Os recursos restantes são eliminados após as duas categorias anteriores.

Depois da ordem é determinada, o Resource Manager emite uma operação de eliminação para cada recurso. Ele aguarda todas as dependências concluir antes de continuar.

Para operações síncronas, são os códigos de resposta com êxito esperado:

* 200
* 204
* 404

Para operações assíncronas, a resposta bem-sucedida esperada é 202. Gestor de recursos controla o cabeçalho de localização ou o cabeçalho de operação do azure-async para determinar o estado da operação assíncrona delete.
  
### <a name="deletion-errors"></a>Erros de eliminação

Quando uma operação de eliminação retorna um erro, o Resource Manager tentará novamente com a chamada de eliminação. As repetições ocorrem para 5xx, 429 e códigos de estado 408. Por predefinição, o período de tempo entre tentativas é de 15 minutos.

## <a name="after-deletion"></a>Após a eliminação

Gestor de recursos emite uma chamada GET para cada recurso que ele estava a tentar eliminar. A resposta a esta chamada GET deve ser 404. Quando o Resource Manager obtém um 404, considera a eliminação para serem concluídos com êxito. Gestor de recursos remove o recurso a partir da respetiva cache.

No entanto, se a chamada GET do recurso retorna um 200 ou 201, o Resource Manager recria o recurso.

Se a operação de obtenção de devolver um erro, o Resource Manager repete GET para o seguinte código de erro:

* Menos de 100
* 408
* 429
* Superior a 500

Para outros códigos de erro, o Resource Manager não consegue a eliminação do recurso.

## <a name="delete-resource-group"></a>Eliminar grupo de recursos

Utilize um dos seguintes métodos para eliminar o grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal,](https://portal.azure.com)selecione o grupo de recursos que pretende eliminar.

1. Selecione **Eliminar grupo de recursos**.

   ![Eliminar grupo de recursos](./media/delete-resource-group/delete-group.png)

1. Para confirmar a eliminação, digite o nome do grupo de recursos

---

## <a name="delete-resource"></a>Eliminar recurso

Utilize um dos seguintes métodos para eliminar um recurso.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal,](https://portal.azure.com)selecione o recurso que pretende eliminar.

1. Selecione **Eliminar**. A imagem seguinte mostra as opções de gestão de uma máquina virtual.

   ![Eliminar recurso](./media/delete-resource-group/delete-resource.png)

1. Quando lhe for perguntado, confirme a eliminação.

---


## <a name="next-steps"></a>Passos seguintes

* Para compreender os conceitos de Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
* Para comandos de eliminação, consulte [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)e [REST API](/rest/api/resources/resourcegroups/delete).
