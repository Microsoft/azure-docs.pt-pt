---
title: Resolução de problemas Erro do Hub Azure IoT 429001 ThrottlingException
description: Entenda como corrigir o erro 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 9619a3d2ba24e806f6c684a5576bce03d7e6b793
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060980"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Este artigo descreve as causas e soluções para **erros de ThrottlingException 429001.**

## <a name="symptoms"></a>Sintomas

Os seus pedidos ao IoT Hub falham com o erro **429001 ThrottlingException**.

## <a name="cause"></a>Causa

Os [limites de estrangulamento](./iot-hub-devguide-quotas-throttling.md) do IoT Hub foram ultrapassados para a operação solicitada.

## <a name="solution"></a>Solução

Verifique se está a atingir o limite de estrangulamento comparando a sua *mensagem de Telemetria, enviando tentativas* de tentativas métricas com os limites acima especificados. Também pode verificar a *métrica do número de erros de estrangulamento.* Para obter informações sobre estas métricas, consulte [as métricas de telemetria do dispositivo](monitor-iot-hub-reference.md#device-telemetry-metrics). Para obter informações sobre como utilizar métricas para ajudá-lo a monitorizar o seu hub IoT, consulte [o Monitor IoT Hub](monitor-iot-hub.md).

O IoT Hub devolve 429 ThrottlingException apenas depois de o limite ter sido violado por um período demasiado longo. Isto é feito para que as suas mensagens não sejam retiradas se o seu hub de IoT tiver tráfego rebentado. Entretanto, o Hub IoT processa as mensagens de acordo com a velocidade de acelerador da operação, que pode ser lenta caso haja demasiado tráfego no registo de tarefas pendentes. Para saber mais, veja [Modelagem de tráfego do Hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Passos seguintes

Considere [aumentar o seu Hub IoT](./iot-hub-scaling.md) se estiver a correr para os limites de quota ou de estrangulamento.