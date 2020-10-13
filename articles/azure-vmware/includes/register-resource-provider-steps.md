---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575746"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Em alternativa, pode utilizar o GUI para registar o fornecedor de recursos **Microsoft.AVS.**  Para obter mais informações, consulte o [fornecedor de recursos do Registo e artigo de tipos.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)  
