---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792099"
---
Pode utilizar a API [Rest Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de saúde dos pontos finais. Recomendamos a utilização das [métricas do IoT Hub relacionadas](../articles/iot-hub/iot-hub-metrics.md) com a latência da mensagem de encaminhamento para identificar e depurar erros quando a saúde do ponto final está morta ou pouco saudável, pois esperamos que a latência seja maior quando o ponto final está num desses estados.


|Estado de Funcionamento|Descrição|
|---|---|
|saudável|O ponto final é aceitar mensagens como esperado.|
|insalubre|O ponto final não está a aceitar mensagens e o IoT Hub está a tentar enviar mensagens para este ponto final.|
|desconhecido|O IoT Hub não tentou entregar mensagens a este ponto final.|
|degradado|O ponto final é aceitar mensagens mais lentas do que o esperado ou está a recuperar de um estado pouco saudável.|
|morto|O IoT Hub já não está a entregar mensagens a este ponto final. As retrótaras para enviar mensagens para este ponto final falharam.|
