---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639122"
---
A maneira mais fácil de instalar extensões de associação é habilitar [pacotes de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Quando você habilita os pacotes, um conjunto predefinido de pacotes de extensão é instalado automaticamente.

Para habilitar os pacotes de extensão, abra o arquivo host. JSON e atualize seu conteúdo para corresponder ao seguinte código:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```