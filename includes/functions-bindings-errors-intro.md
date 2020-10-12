---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77474173"
---
Os erros levantados numa Função Azure podem vir de qualquer uma das seguintes origens:

- Utilização de funções Azure [incorporadas e encadernações](..\articles\azure-functions\functions-triggers-bindings.md)
- Chamadas para APIs dos serviços Azure subjacentes
- Chamadas para pontos finais REST
- Chamadas para bibliotecas de clientes, pacotes ou APIs de terceiros

Seguir práticas sólidas de manuseamento de erros é importante evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de tratamento de erros incluem as seguintes ações:

- [Ativar o Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Utilizar o tratamento de erros estruturados](#use-structured-error-handling)
- [Design para idempotency](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de reecaçação](../articles/azure-functions/functions-reliable-event-processing.md) (se for caso disso)

### <a name="use-structured-error-handling"></a>Utilizar o tratamento de erros estruturados

Capturar e publicar erros é fundamental para monitorizar a saúde da sua aplicação. O nível mais alto de qualquer código de função deve incluir um bloco de tentativa/captura. No bloco de capturas, pode capturar e publicar erros.

### <a name="retry-support"></a>Apoio de retíria

Os seguintes gatilhos têm suporte de retíria incorporado:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de Filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Autocarro de serviço Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, estes gatilhos relemquem pedidos até cinco vezes. Após a quinta repetição, tanto o armazenamento da Fila Azure como o Azure Service Bus disparam escrever uma mensagem para uma [fila de venenos](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages).

É necessário implementar manualmente políticas de repetição para quaisquer outros tipos de gatilhos ou encadernações. As implementações manuais podem incluir a redação de informações de erro para uma [fila de mensagens venenosas](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Ao escrever para uma fila de venenos, você tem a oportunidade de voltar a tentar operações mais tarde. Esta abordagem é a mesma usada pelo gatilho de armazenamento Blob.
