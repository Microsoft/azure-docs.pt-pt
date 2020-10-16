---
title: Orientação de manuseamento de erros de funções Azure Functions
description: Aprenda a lidar com erros em Funções Azure com ligações a erros de ligação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0617d55f7c67c788b1e898d963f7d509cef72d49
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096849"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros de funções Azure

O manuseamento de erros nas Funções Azure é importante para evitar dados perdidos, eventos perdidos e monitorizar a saúde da sua aplicação.

Este artigo descreve estratégias gerais de manipulação de erros, juntamente com ligações a erros específicos de ligação.

## <a name="handling-errors"></a>Processar erros

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Códigos de erro de ligação

Ao integrar-se com os serviços Azure, os erros podem ter origem nas APIs dos serviços subjacentes. As informações relativas a erros específicos vinculativos estão disponíveis na secção **"Exceções" e "códigos de devolução"** dos seguintes artigos:

+ [BD do Cosmos para o Azure](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
