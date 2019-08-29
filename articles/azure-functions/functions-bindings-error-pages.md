---
title: Diretrizes de tratamento de erro Azure Functions | Microsoft Docs
description: Fornece diretrizes gerais para lidar com erros que ocorrem quando suas funções são executadas e links para tópicos de erros específicos de associação.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: fdfee3442986322f242da730bb9ceccbc9f9e250
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097492"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erro Azure Functions

Este tópico fornece diretrizes gerais para lidar com erros que ocorrem quando suas funções são executadas. Ele também fornece links para os tópicos que descrevem erros específicos de associação que podem ocorrer. 

## <a name="handling-errors-in-functions"></a>Tratamento de erros em funções
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao integrar com os serviços do Azure, você pode ter erros gerados que se originam das APIs dos serviços subjacentes. Links para a documentação do código de erro para esses serviços podem ser encontrados na seção **exceções e códigos de retorno** dos seguintes tópicos de referência de gatilho e de associação:

+ [BD do Cosmos para o Azure](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
