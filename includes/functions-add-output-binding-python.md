---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191081"
---
Atualização *HttpExample\\\_\_init\_\_.py* para combinar com o seguinte código, `if name:` adicionando o `msg` parâmetro à definição de função e `msg.set(name)` sob a declaração.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O `msg` parâmetro é um [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)exemplo do . O `set` seu método escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.
