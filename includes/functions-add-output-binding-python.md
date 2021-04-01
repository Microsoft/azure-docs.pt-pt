---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78191081"
---
Atualização *HttpExample \\ \_ \_ init \_ \_ .py* para corresponder ao seguinte código, adicionando o `msg` parâmetro à definição de função e `msg.set(name)` sob a `if name:` declaração.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O `msg` parâmetro é um exemplo do [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . O seu `set` método escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.
