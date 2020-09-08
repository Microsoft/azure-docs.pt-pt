---
title: Registe-se o fornecedor de recursos Azure VMware Solution
description: Passos para registar o fornecedor de recursos Azure VMware Solution.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512381"
---
Para utilizar a Solução Azure VMware, tem primeiro de registar o fornecedor de recursos com a sua subscrição.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md).