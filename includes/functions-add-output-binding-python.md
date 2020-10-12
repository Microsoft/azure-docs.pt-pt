---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191081"
---
Atualizar *HttpExample \\ \_ \_ init \_ \_ .py* para corresponder ao seguinte código, adicionando o `msg` parâmetro à definição de função e sob a `msg.set(name)` `if name:` declaração.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O `msg` parâmetro é um exemplo do [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . O seu `set` método escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.
