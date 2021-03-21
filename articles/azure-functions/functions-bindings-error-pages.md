---
title: Azure Funções manipulação de erros e orientação de retagem
description: Aprenda a lidar com erros e a tentar novamente eventos em Funções Azure com ligações a erros de ligação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93284450"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions manipulação de erros e retrações

O manuseamento de erros nas Funções Azure é importante para evitar dados perdidos, eventos perdidos e monitorizar a saúde da sua aplicação.

Este artigo descreve estratégias gerais de manipulação de erros, juntamente com ligações a erros específicos de ligação.

## <a name="handling-errors"></a>Processar erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Códigos de erro de ligação

Ao integrar-se com os serviços Azure, os erros podem ter origem nas APIs dos serviços subjacentes. As informações relativas a erros específicos vinculativos estão disponíveis na secção **"Exceções" e "códigos de devolução"** dos seguintes artigos:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
