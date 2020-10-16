---
title: Eliminar grupo de recursos e recursos
description: Descreve como eliminar grupos de recursos e recursos. Descreve como o Azure Resource Manager ordena a supressão de recursos quando um grupo de recursos elimina um grupo de recursos. Descreve os códigos de resposta e como o Gestor de Recursos os trata para determinar se a eliminação foi bem sucedida.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: d6c38169916cc6c47d6f05c88645fa435bbdb146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614415"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Grupo de recursos Azure Resource Manager e eliminação de recursos

Este artigo mostra como eliminar grupos de recursos e recursos. Descreve como o Azure Resource Manager ordena a supressão de recursos quando elimina um grupo de recursos.

## <a name="how-order-of-deletion-is-determined"></a>Como a ordem de supressão é determinada

Quando elimina um grupo de recursos, o Gestor de Recursos determina a ordem para eliminar recursos. Utiliza a seguinte ordem:

1. Todos os recursos da criança (aninhados) são eliminados.

2. Os recursos que gerem outros recursos são eliminados em seguida. Um recurso pode ter a `managedBy` propriedade definida para indicar que um recurso diferente o gere. Quando esta propriedade é definida, o recurso que gere o outro recurso é eliminado antes dos outros recursos.

3. Os restantes recursos são suprimidos após as duas categorias anteriores.

Após a determinação da encomenda, o Gestor de Recursos emite uma operação DELETE para cada recurso. Espera que quaisquer dependências terminem antes de prosseguir.

Para operações sincronizadas, os códigos de resposta esperados são:

* 200
* 204
* 404

Para operações assíncronos, a resposta esperada para o sucesso é 202. O Gestor de Recursos rastreia o cabeçalho de localização ou o cabeçalho de operação azure-async para determinar o estado da operação de eliminação assíncrona.
  
### <a name="deletion-errors"></a>Erros de eliminação

Quando uma operação de eliminação retorna um erro, o Gestor de Recursos retrição a chamada DELETE. As retrações acontecem para os códigos de estado 5xx, 429 e 408. Por predefinição, o período de tempo para a repetição é de 15 minutos.

## <a name="after-deletion"></a>Após a eliminação

O Gestor de Recursos emite uma chamada GET em cada recurso que tentou apagar. Espera-se que a resposta desta chamada GET seja 404. Quando o Gestor de Recursos obtém um 404, considera que a eliminação foi concluída com sucesso. O Gestor de Recursos remove o recurso da sua cache.

No entanto, se a chamada GET sobre o recurso devolver um 200 ou 201, o Gestor de Recursos recria o recurso.

Se a operação GET retornar um erro, o Gestor de Recursos retrição o GET para o seguinte código de erro:

* Menos de 100
* 408
* 429
* Maior que 500

Para outros códigos de erro, o Gestor de Recursos falha na supressão do recurso.

> [!IMPORTANT]
> A supressão do Grupo de Recursos é irreversível.

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

1. Selecione **Eliminar**. A imagem que se segue mostra as opções de gestão de uma máquina virtual.

   ![Eliminar recurso](./media/delete-resource-group/delete-resource.png)

1. Quando lhe for perguntado, confirme a eliminação.

---


## <a name="next-steps"></a>Passos seguintes

* Para compreender os conceitos de Gestor de Recursos, consulte [a visão geral do Azure Resource Manager](overview.md).
* Para comandos de supressão, consulte [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group#az-group-delete)e [REST API](/rest/api/resources/resourcegroups/delete).
