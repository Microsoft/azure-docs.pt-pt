---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770838"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição.  

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