---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68639122"
---
A forma mais fácil de instalar extensões de ligação é permitir [pacotes de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Quando ativa os pacotes, é automaticamente instalado um conjunto de pacotes de extensão predefinido.

Para permitir pacotes de extensão, abra o host.jsno ficheiro e atualize o seu conteúdo para corresponder ao seguinte código:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```