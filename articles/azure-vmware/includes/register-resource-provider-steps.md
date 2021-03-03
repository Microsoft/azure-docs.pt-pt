---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750971"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição. Para obter mais informações sobre os fornecedores de recursos, consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>CLI do Azure 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Portal do Azure
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **de todos os serviços,** **introduza a subscrição** e, em seguida, selecione **Subscrições**.

1. Selecione a subscrição da lista de subscrição para visualizar.

1. Selecione **fornecedores de recursos** e introduza **o Microsoft.AVS** na pesquisa. 
 
1. Se o fornecedor de recursos não estiver registado, **selecione Registar-se**.