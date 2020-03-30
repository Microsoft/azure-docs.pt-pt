---
title: Orientação de manipulação de erros das Funções Azure
description: Aprenda a lidar com erros em Funções Azure com ligações a erros de ligação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586194"
---
# <a name="azure-functions-error-handling"></a>Manipulação de erros das Funções Azure

O manuseamento de erros nas Funções Azure é importante para evitar dados perdidos, eventos perdidos e monitorizar a saúde da sua aplicação.

Este artigo descreve estratégias gerais de manipulação de erros juntamente com ligações a erros específicos de ligação.

## <a name="handling-errors"></a>Processar erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Códigos de erro de ligação

Ao integrar os serviços Azure, os erros podem ter origem nas APIs dos serviços subjacentes. As informações relativas a erros específicos de ligação estão disponíveis na secção **exceções e códigos** de devolução dos seguintes artigos:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de eventos](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de fila](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
