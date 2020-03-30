---
title: Ligação de saída do Hub Azure IoT para funções azure
description: Aprenda a escrever mensagens para os streams do Azure IoT Hubs utilizando funções azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277430"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Ligação de saída do Hub Azure IoT para funções azure

Este artigo explica como trabalhar com as ligações de saída da Azure Functions para o IoT Hub. O suporte ioT Hub baseia-se na [Ligação de Hubs de Eventos Azure.](functions-bindings-event-hubs.md)

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Enquanto as seguintes amostras de código utilizam a API do Hub de Eventos, a sintaxe dada é aplicável às funções do Hub IoT.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Passos seguintes

- [Responda a eventos enviados para um fluxo de eventos de eventos (Trigger)](./functions-bindings-event-iot-trigger.md)
