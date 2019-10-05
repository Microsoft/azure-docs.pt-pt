---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 178fa7d5f129a12736ec068fca605ba24cd37839
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955875"
---
Os erros gerados em um Azure Functions podem vir de qualquer uma das seguintes origens:

- Uso de [gatilhos e associações](..\articles\azure-functions\functions-triggers-bindings.md) de Azure Functions internos
- Chamadas para APIs de serviços subjacentes do Azure
- Chamadas para pontos de extremidade REST
- Chamadas para bibliotecas de cliente, pacotes ou APIs de terceiros

Seguir as práticas sólidas de tratamento de erros é importante para evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de tratamento de erros incluem as seguintes ações:

- [Habilitar Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usar tratamento de erro estruturado](#use-structured-error-handling)
- [Design para Idempotência](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de repetição](../articles/azure-functions/functions-reliable-event-processing.md) (quando apropriado)

### <a name="use-structured-error-handling"></a>Usar tratamento de erro estruturado

Capturar e publicar erros é essencial para monitorar a integridade do seu aplicativo. O nível mais alto de qualquer código de função deve incluir um bloco try/catch. No bloco catch, você pode capturar e publicar erros.

### <a name="retry-support"></a>Tentar suporte novamente

Os seguintes gatilhos têm suporte interno para repetição:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Barramento de serviço do Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, esses gatilhos retentam solicitações até cinco vezes. Após a quinta repetição, os dois gatilhos gravam uma mensagem em uma [fila suspeita](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Você precisa implementar manualmente as políticas de repetição para quaisquer outros tipos de associações ou disparadores. Implementações manuais podem incluir a gravação de informações de erro em uma [fila de mensagens suspeitas](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Ao gravar em uma fila suspeita, você tem a oportunidade de tentar novamente as operações mais tarde. Essa abordagem é a mesma usada pelo gatilho do armazenamento de BLOBs.
