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
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737235"
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

Abra o ficheiro de Function para sua nova função num editor de texto, atualize o **authLevel** propriedade na **enlaces** para `anonymous`e guarde as alterações.

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
