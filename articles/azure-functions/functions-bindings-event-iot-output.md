---
title: Ligação de saída do Azure IoT Hub para funções Azure
description: Aprenda a escrever mensagens para streams Azure IoT Hubs usando Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871784"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Ligação de saída do Azure IoT Hub para funções Azure

Este artigo explica como trabalhar com as ligações de saída da Azure Functions para o IoT Hub. O suporte IoT Hub baseia-se na [Ligação Azure Event Hubs](functions-bindings-event-hubs.md).

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Embora as seguintes amostras de código utilizem a API do Centro de Eventos, a sintaxe dada é aplicável para as funções IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Passos seguintes

- [Responda aos eventos enviados para um stream de eventos hub (Trigger)](./functions-bindings-event-iot-trigger.md)
