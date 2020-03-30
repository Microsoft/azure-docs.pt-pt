---
title: Problemas de resolução de problemas Azure IoT Hub erro 403004 DispositivoMaximumQueueDepthExceeded
description: Entenda como corrigir o erro 403004 DispositivoMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497490"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Este artigo descreve as causas e soluções para **erros 403004 DispositivoMaximumDepthDepthDepthExceeded.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem cloud-to-device, o pedido falha com o erro **403004** ou **dispositivoMaximumDepthDepthExceeded**.

## <a name="cause"></a>Causa

A causa subjacente é que o número de mensagens enquecidas para o dispositivo excede o limite de [fila (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

A razão mais provável para estar a escorrer para este limite é porque está `ReceiveAsync`a usar https para receber a mensagem, o que leva a uma votação contínua usando , resultando em IoT Hub a estrangular o pedido.

## <a name="solution"></a>Solução

O padrão suportado para mensagens cloud-to-device com HTTPS é intermitentemente conectado dispositivos que verificam mensagens com pouca frequência (menos de 25 minutos). Para reduzir a probabilidade de correr para o limite de fila, mude para AMQP ou MQTT para mensagens cloud-to-device.

Alternativamente, melhore a lógica lateral do dispositivo para completar, rejeitar ou abandonar rapidamente as mensagens em fila, encurtar o tempo de vida ou considerar enviar menos mensagens. Veja [Vida útil das mensagens C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Por último, considere utilizar a [API](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) da Fila de Purga para limpar periodicamente as mensagens pendentes antes de o limite ser atingido.