---
title: Documentação de orientação de tratamento de erros de funções de Azure | Documentos da Microsoft
description: Fornece orientações gerais para a manipulação de erros que ocorrem no quando executar as suas funções e ligações para tópicos de erros de ligação específica.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480716"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros de funções do Azure

Este tópico fornece orientações gerais para o tratamento de erros que ocorrem ao executar as suas funções. Ele também fornece ligações para os tópicos que descrevem os erros de ligação específicos que podem ocorrer. 

## <a name="handing-errors-in-functions"></a>Erros nas funções de processamento
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de erro de enlace

Ao integrar com serviços do Azure, pode ter os erros levantados provenientes as APIs dos serviços subjacentes. Documentação de código de ligações para o erro para estes serviços podem ser encontrados no **exceções e códigos de retorno** secção das seguinte acionador e tópicos de referência de ligação:

+ [BD do Cosmos para o Azure](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
