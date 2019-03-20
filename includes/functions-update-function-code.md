---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 3b0be31afbce78c0de8dc919245e92bee7af04b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964247"
---
## <a name="update-the-function"></a>Atualizar a função

Por predefinição, o modelo cria uma função que requer uma tecla de função ao efetuar pedidos. Para que seja mais fácil testar a função no Azure, terá de atualizar a função para permitir acesso anónimo. A forma como efetua esta alteração depende da sua linguagem de projeto de funções.

### <a name="c"></a>C\#

Abra o ficheiro de código MyHttpTrigger.cs que é a sua nova função e atualize o atributo **AuthorizationLevel** na definição de função para um valor de `Anonymous` e guarde as alterações.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Abra o ficheiro function.json da sua nova função, abra-o num editor de texto, atualize a propriedade **authLevel** em **bindings.httpTrigger** para `anonymous` e guarde as alterações.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Agora, pode chamar a função no Azure sem ter de indicar a tecla de função. A tecla de função nunca é necessária na execução local.
