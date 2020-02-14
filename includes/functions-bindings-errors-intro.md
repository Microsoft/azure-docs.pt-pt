---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8573a915c572885b53437843b114aa987be4c66b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198462"
---
Os erros levantados numa Funções Azure podem vir de qualquer uma das seguintes origens:

- Utilização de funções azure [incorporadas dispara e encaderna](..\articles\azure-functions\functions-triggers-bindings.md)
- Chamadas para APIs dos serviços Azure subjacentes
- Chamadas para pontos finais REST
- Chamadas para bibliotecas, pacotes ou APIs de terceiros

Seguir práticas sólidas de manipulação de erros é importante para evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de manipulação de erros incluem as seguintes ações:

- [Ativar insights de aplicação](../articles/azure-functions/functions-monitoring.md)
- [Utilizar o manuseamento de erros estruturados](#use-structured-error-handling)
- [Design para idempotency](../articles/azure-functions/functions-idempotent.md)
- Implementar políticas de [retry](../articles/azure-functions/functions-reliable-event-processing.md) (se for caso disso)

### <a name="use-structured-error-handling"></a>Utilizar o manuseamento de erros estruturados

Capturar e publicar erros é fundamental para monitorizar a saúde da sua aplicação. O nível mais alto de qualquer código de função deve incluir um bloco de tentativa/captura. No bloco de captura, pode capturar e publicar erros.

### <a name="retry-support"></a>Apoio de retry

Os seguintes gatilhos têm suporte integrado de retry:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de fila azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Ônibus de serviço Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, estes gatilhos retentam pedidos até cinco vezes. Após a quinta tentativa, tanto o armazenamento da Fila Azure como os gatilhos do Azure Service Bus escrevem uma mensagem para uma [fila de venenos.](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)

É necessário implementar manualmente políticas de retry para quaisquer outros gatilhos ou tipos de encadernação. As implementações manuais podem incluir a escrita de informações de erro para uma fila de [mensagens venenosas](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Ao escrever para uma fila de venenos, tem a oportunidade de voltar a tentar operações mais tarde. Esta abordagem é a mesma utilizada pelo gatilho de armazenamento Blob.
