---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132474"
---
As funções do Azure [acionadores e enlaces](../articles/azure-functions/functions-triggers-bindings.md) comunicar com vários serviços do Azure. Ao integrar-se com estes serviços, pode ter os erros levantados provenientes as APIs dos serviços do Azure subjacentes. Também podem ocorrer erros quando tenta comunicar com outros serviços a partir do código de função utilizando as bibliotecas de cliente ou REST. Para evitar perda de dados e garantir o bom comportamento das suas funções, é importante processar erros da origem.

Os acionadores seguintes têm suporte de repetição incorporado:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [O Azure Service Bus (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por predefinição, estes acionadores são repetidas até cinco vezes. Depois de repetir o quinto, estes acionadores gravar uma mensagem para um especial [fila não processáveis](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages).

Para outros acionadores de funções, não existe nenhuma repetição incorporada quando ocorrerem erros durante a execução de função. Para evitar a perda de informações de Acionador deve ocorrer um erro na sua função, é recomendável que usar blocos try-catch no código da função para identificar quaisquer erros. Quando ocorre um erro, escrever as informações transmitidas para a função pelo acionador para uma fila de mensagens "não processáveis" especial. Essa abordagem é a mesma usada pela [acionador do armazenamento de BLOBs](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

Dessa forma, pode capturar os eventos de Acionador que poderiam ser perdido devido a erros e repeti-las mais tarde com outra função para processar mensagens na fila não processáveis usando as informações armazenadas.  
