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
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960909"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DispositivoMaximumDepthDepthExceeded

Este artigo descreve as causas e soluções para **erros 403004 DispositivoMaximumDepthDepthDepthExceeded.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem cloud-to-device, o pedido falha com o erro **403004** ou **dispositivoMaximumDepthDepthExceeded**.

## <a name="cause"></a>Causa

A causa subjacente é que o número de mensagens enquecidas para o dispositivo excede o limite de [fila (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

A razão mais provável para estar a escorrer para este limite é porque está a usar https para receber a mensagem, o que leva a uma votação contínua usando `ReceiveAsync`, resultando em IoT Hub a estrangular o pedido.

## <a name="solution"></a>Solução

O padrão suportado para mensagens cloud-to-device com HTTPS é intermitentemente conectado dispositivos que verificam mensagens com pouca frequência (menos de 25 minutos). Para reduzir a probabilidade de correr para o limite de fila, mude para AMQP ou MQTT para mensagens cloud-to-device.

Alternativamente, melhore a lógica lateral do dispositivo para completar, rejeitar ou abandonar rapidamente as mensagens em fila, encurtar o tempo de vida ou considerar enviar menos mensagens. Veja [Vida útil das mensagens C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Por último, considere utilizar a [API](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) da Fila de Purga para limpar periodicamente as mensagens pendentes antes de o limite ser atingido.