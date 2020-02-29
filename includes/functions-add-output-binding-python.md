---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191081"
---
Update *HttpExample\\\_\_init\_\_.py* para combinar com o seguinte código, adicionando o parâmetro `msg` à definição de função e `msg.set(name)` sob a declaração `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O parâmetro `msg` é um exemplo do [`azure.functions.InputStream class`. ](/python/api/azure-functions/azure.functions.httprequest) O seu método `set` escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.
