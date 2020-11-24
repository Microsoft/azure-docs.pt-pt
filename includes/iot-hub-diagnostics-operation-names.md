---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564282"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nome da Operação|Nível|Descrição|
|------------- |-----|-----------|
|Não definitadoRouteevaluation|Informações|A mensagem não pode ser avaliada com uma condição de doação. Por exemplo, se um imóvel na condição de consulta de rota estiver ausente na mensagem. Saiba mais sobre [a sintaxe de](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)consulta de encaminhamento .|
|RouteEvaluationError|Erro|Houve um erro na avaliação da mensagem devido a um problema com o formato da mensagem. Por exemplo, este erro será registado se o conteúdo codificar não for especificado ou o tipo de conteúdo não for válido na mensagem. Estes devem ser definidos nas propriedades do [sistema.](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)|
|Deixou cair a Esmissão|Erro|A mensagem foi deixada e não foi encaminhada. Isto pode ser devido a razões como a mensagem não corresponde a qualquer consulta de encaminhamento ou ponto final estava morto e a mensagem não podia ser entregue após várias retraçãos. Recomendamos obter mais detalhes sobre o ponto final utilizando a API REST [obter saúde de ponta.](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)|
|EndpointUnhealthy|Erro|Endpoint não tem aceitado mensagens do IoT Hub e ioT Hub está a tentar reensundirar as mensagens. Recomendamos observar o último erro conhecido através da API REST [obter saúde de ponta.](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)|
|EndpointDead|Erro|O Endpoint não aceita mensagens do IoT Hub há mais de uma hora. Recomendamos observar o último erro conhecido através da API REST [obter saúde de ponta.](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)|
|EndpointHealthy|Informações|Endpoint é saudável e recebe mensagens do IoT Hub. Esta mensagem não é registada continuamente, mas registada apenas quando o ponto final se tornar saudável novamente. Esta mensagem significa que o IoT Hub não foi capaz de enviar mensagens para o ponto final, mas o ponto final está agora saudável.|
|Órfão|Informações|A mensagem não corresponde a nenhuma rota.|
|InvalidMessage|Erro|A mensagem é inválida devido à incompatibilidade com o ponto final. Recomendamos configurações de verificação do ponto final.|


As operações *UndefinedRouteEvaluation*, *RouteEvaluationError* e *OrphanedMessage* são estranguladas e registadas não mais do que uma vez por minuto por IoT Hub.