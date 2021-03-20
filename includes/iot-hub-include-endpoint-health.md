---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95557070"
---
Pode utilizar a API [Rest Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de saúde dos pontos finais. Recomendamos a utilização das [métricas de encaminhamento do IoT Hub relacionadas](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) com a latência da mensagem de encaminhamento para identificar e depurar erros quando a saúde do ponto final está morta ou pouco saudável, pois esperamos que a latência seja maior quando o ponto final está num desses estados. Para saber mais sobre a utilização de métricas do IoT Hub, consulte [o Monitor IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Estado de Funcionamento|Description|
|---|---|
|saudável|O ponto final é aceitar mensagens como esperado.|
|insalubre|O ponto final não está a aceitar mensagens e o IoT Hub está a tentar enviar mensagens para este ponto final.|
|desconhecido|O IoT Hub não tentou entregar mensagens a este ponto final.|
|degradado|O ponto final é aceitar mensagens mais lentas do que o esperado ou está a recuperar de um estado pouco saudável.|
|morto|O IoT Hub já não está a entregar mensagens a este ponto final. As retrótaras para enviar mensagens para este ponto final falharam.|