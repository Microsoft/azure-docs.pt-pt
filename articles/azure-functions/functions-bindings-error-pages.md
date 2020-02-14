---
title: Orientação de manipulação de erros das Funções Azure
description: Aprenda a lidar com erros em Funções Azure com ligações a erros de ligação específicos.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 920b887382ca55d84c66fabb8f1464fd05041094
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198463"
---
# <a name="azure-functions-error-handling"></a>Manipulação de erros das Funções Azure

O manuseamento de erros nas Funções Azure é importante para evitar dados perdidos, eventos perdidos e monitorizar a saúde da sua aplicação.

Este artigo descreve estratégias gerais de manipulação de erros juntamente com ligações a erros específicos de ligação.

## <a name="handling-errors"></a>Erros de manuseamento

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Códigos de erro de ligação

Ao integrar os serviços Azure, os erros podem ter origem nas APIs dos serviços subjacentes. As informações relativas a erros específicos de ligação estão disponíveis na secção **exceções e códigos** de devolução dos seguintes artigos:

+ [BD do Cosmos para o Azure](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de blobs](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hubs de Eventos](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)
