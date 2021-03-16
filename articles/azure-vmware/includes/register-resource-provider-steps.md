---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555913"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição. Para obter mais informações sobre os fornecedores de recursos, consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **de todos os serviços,** **introduza a subscrição** e, em seguida, selecione **Subscrições**.

1. Selecione a subscrição da lista de subscrição para visualizar.

1. Selecione **fornecedores de recursos** e introduza **o Microsoft.AVS** na pesquisa. 
 
1. Se o fornecedor de recursos não estiver registado, **selecione Registar-se**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Inscreva-se na subscrição Azure que utiliza para a implementação da Solução Azure VMware através do Azure CLI. Registe o `Microsoft.AVS` fornecedor de recursos com o comando de registo do fornecedor [az:](/cli/azure/provider#az_provider_register)

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Pode utilizar o comando [da lista de fornecedores az](/cli/azure/provider#az_provider_list) para ver todos os fornecedores disponíveis.

---


 
