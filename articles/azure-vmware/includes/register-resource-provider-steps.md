---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653185"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição. Para obter mais informações sobre os fornecedores de recursos, consulte [os fornecedores e tipos de recursos Azure](/azure/azure-resource-manager/management/resource-providers-and-types).

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
