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
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548099"
---
Pode utilizar a API [Rest Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de saúde dos pontos finais. Recomendamos a utilização das [métricas de encaminhamento do IoT Hub relacionadas](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) com a latência da mensagem de encaminhamento para identificar e depurar erros quando a saúde do ponto final está morta ou pouco saudável, pois esperamos que a latência seja maior quando o ponto final está num desses estados. Para saber mais sobre a utilização de métricas do IoT Hub, consulte [o Monitor IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Estado de Funcionamento|Descrição|
|---|---|
|saudável|O ponto final é aceitar mensagens como esperado.|
|insalubre|O ponto final não está a aceitar mensagens e o IoT Hub está a tentar enviar mensagens para este ponto final.|
|desconhecido|O IoT Hub não tentou entregar mensagens a este ponto final.|
|degradado|O ponto final é aceitar mensagens mais lentas do que o esperado ou está a recuperar de um estado pouco saudável.|
|morto|O IoT Hub já não está a entregar mensagens a este ponto final. As retrótaras para enviar mensagens para este ponto final falharam.|
