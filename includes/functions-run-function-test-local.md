---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184033"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

O comando seguinte inicia a aplicação de funções. A aplicação é executada com o mesmo runtime de Funções do Azure que está no Azure.

```bash
func host start --build
```

A opção `--build` é necessária para compilar projetos C#. Não precisa desta opção para um projeto de JavaScript.

Quando o anfitrião de Funções começar, será apresentado algo semelhante ao resultado seguinte, que foi truncado para facilitar a leitura:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie o URL da função `HttpTrigger` do resultado do runtime e cole-o na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função local:

![Testar localmente no browser](./media/functions-run-function-test-local/functions-test-local-browser.png)

Agora que executou a função localmente, pode criar a aplicação de funções e outros recursos necessários no Azure.