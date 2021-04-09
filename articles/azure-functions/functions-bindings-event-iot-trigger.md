---
title: Azure IoT Hub trigger for Azure Functions
description: Aprenda a responder aos eventos enviados para um stream de eventos IoT hub em Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612291"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger for Azure Functions

Este artigo explica como trabalhar com as ligações Azure Functions para o IoT Hub. O suporte IoT Hub baseia-se na [Ligação Azure Event Hubs](functions-bindings-event-hubs.md).

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Embora as seguintes amostras de código utilizem a API do Centro de Eventos, a sintaxe dada é aplicável para as funções IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.jssobre propriedades

A [host.jsno](functions-host-json.md#eventhub) ficheiro contém definições que controlam o comportamento do Centro de Eventos. Consulte a secçãohost.jsna secção [ de definições](functions-bindings-event-iot.md#hostjson-settings) para obter informações sobre as definições disponíveis.

## <a name="next-steps"></a>Passos seguintes

- [Escreva eventos para um fluxo de eventos (ligação de saída)](./functions-bindings-event-iot-output.md)
