---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 380cb513ec389293db757e667ed4681778e29348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254648"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Em alternativa, pode utilizar o GUI para registar o fornecedor de recursos **Microsoft.AVS.**  Para obter mais informações, consulte o [fornecedor de recursos do Registo e artigo de tipos.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)  
