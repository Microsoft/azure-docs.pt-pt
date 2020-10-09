---
title: Ligações Azure IoT Hub para Funções Azure
description: Aprenda a usar o gatilho do Hub IoT e a encadernação em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586139"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Ligações Azure IoT Hub para Funções Azure

Este conjunto de artigos explica como trabalhar com as ligações Azure Functions para o IoT Hub. O suporte IoT Hub baseia-se na [Ligação Azure Event Hubs](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Embora as seguintes amostras de código utilizem a API do Centro de Eventos, a sintaxe dada é aplicável para as funções IoT Hub.

| Ação | Tipo |
|--------|------|
| Responda aos eventos enviados para um fluxo de eventos ioT hub. | [Acionador](./functions-bindings-event-iot-trigger.md) |
| Escreva eventos para um stream de eventos IoT | [Ligação de saída](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passos seguintes

- [Responda aos eventos enviados para um stream de eventos hub (Trigger)](./functions-bindings-event-iot-trigger.md)
- [Escreva eventos para um fluxo de eventos (ligação de saída)](./functions-bindings-event-iot-output.md)
