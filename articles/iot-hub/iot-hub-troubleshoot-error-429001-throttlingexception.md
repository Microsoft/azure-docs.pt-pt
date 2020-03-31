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
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960701"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Este artigo descreve as causas e soluções para **erros de 429001 ThrotlingException.**

## <a name="symptoms"></a>Sintomas

Os seus pedidos ao IoT Hub falham com o erro **429001 ThrottlingException**.

## <a name="cause"></a>Causa

Os limites de estrangulamento do IoT Hub foram [ultrapassados](./iot-hub-devguide-quotas-throttling.md) para a operação solicitada.

## <a name="solution"></a>Solução

Verifique se está a atingir o limite de estrangulamento comparando a sua *mensagem telemetria enviar métricas* de tentativas com os limites acima especificados. Também pode verificar a métrica *do número de erros de estrangulamento.* Para obter mais informações sobre estas e outras métricas disponíveis para ioT Hub, consulte [as métricas do IoT Hub e como usá-las](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub devolve 429 ThrotlingExcep apenas depois de o limite ter sido violado por um período demasiado longo. Isto é feito para que as suas mensagens não sejam retiradas se o seu centro de IoT ficar com o tráfego rebentado. Entretanto, o Hub IoT processa as mensagens de acordo com a velocidade de acelerador da operação, que pode ser lenta caso haja demasiado tráfego no registo de tarefas pendentes. Para saber mais, veja [Modelagem de tráfego do Hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Passos seguintes

Considere [escalar o seu Hub IoT](./iot-hub-scaling.md) se estiver a escorrer para os limites de quota ou de estrangulamento.