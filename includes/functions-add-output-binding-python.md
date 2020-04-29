---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191081"
---
Atualização *HttpExample\\\_\_init\_\_.py* para combinar com o seguinte código, `if name:` adicionando o `msg` parâmetro à definição de função e `msg.set(name)` sob a declaração.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O `msg` parâmetro é um [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)exemplo do . O `set` seu método escreve uma mensagem de corda para a fila, neste caso o nome passou para a função na cadeia de consulta URL.
