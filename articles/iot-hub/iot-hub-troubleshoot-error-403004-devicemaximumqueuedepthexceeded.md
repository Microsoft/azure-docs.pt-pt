---
title: Resolução de problemas Erro do Hub Azure IoT 403004 DispositivoMaximumQueueDepthExceed
description: Entenda como corrigir erro 403004 DispositivoMaximumQueueDepthEded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148237"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Este artigo descreve as causas e soluções para **403004 DeviceMaximumQueueDepthExceeded.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem nuvem-para-dispositivo, o pedido falha com o erro **403004** ou **o DeviceMaximumQueueDepthExceed .**

## <a name="cause"></a>Causa

A causa subjacente é que o número de mensagens encadeadas para o dispositivo excede o limite de [fila (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

A razão mais provável para estar a entrar neste limite é porque está a usar https para receber a mensagem, o que leva a uma votação contínua utilizando `ReceiveAsync` , resultando em estrangulamento do IoT Hub.

## <a name="solution"></a>Solução

O padrão suportado para mensagens nuvem-a-dispositivo com HTTPS é dispositivos intermitentemente ligados que verificam mensagens com pouca frequência (menos de 25 em 25 minutos). Para reduzir a probabilidade de correr para o limite da fila, mude para AMQP ou MQTT para mensagens nuvem-dispositivo.

Em alternativa, melhore a lógica do lado do dispositivo para completar, rejeitar ou abandonar mensagens em fila rapidamente, encurtar o tempo de vida ou considerar o envio de menos mensagens. Veja [Vida útil das mensagens C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Por último, considere utilizar a [API de purga para](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) limpar periodicamente as mensagens pendentes antes de atingir o limite.