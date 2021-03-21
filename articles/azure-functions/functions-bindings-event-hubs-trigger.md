---
title: Azure Event Hubs gatilho para funções Azure
description: Aprenda a utilizar o gatilho Azure Event Hubs em Funções Azure.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608929"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Event Hubs gatilho para funções Azure

Este artigo explica como trabalhar com o gatilho [Azure Event Hubs](../event-hubs/event-hubs-about.md) para funções Azure. As funções Azure [suportam ligações](functions-bindings-event-hubs-output.md) de gatilho e saída para os Centros de Eventos.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.jsnas definições

A [host.jsno](functions-host-json.md#eventhub) ficheiro contém definições que controlam o comportamento do Centro de Eventos. Consulte a secçãohost.jsna secção [ de definições](functions-bindings-event-hubs.md#hostjson-settings) para obter informações sobre as definições disponíveis.

## <a name="next-steps"></a>Passos seguintes

- [Escreva eventos para um fluxo de eventos (ligação de saída)](./functions-bindings-event-hubs-output.md)
