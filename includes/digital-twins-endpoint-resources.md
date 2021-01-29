---
author: baanders
description: incluir arquivo para os recursos que são necessários para criar pontos finais Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054517"
---
### <a name="prerequisite-create-endpoint-resources"></a>Pré-requisito: Criar recursos de ponto final

Para ligar um ponto final à Azure Digital Twins, o tópico da grelha de eventos, o centro de eventos ou o tópico do Service Bus que está a usar para o ponto final já precisa de existir.

Utilize o gráfico seguinte para ver que recursos devem ser criados antes de criar o seu ponto final.

| Tipo de ponto final | Recursos necessários (ligados às instruções de criação) |
| --- | --- |
| Ponto final da Grelha de Eventos | [tópico de grelha de evento](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Ponto final do Event Hubs | [Espaço &nbsp; de nomes de Centros de &nbsp; Eventos](../articles/event-hubs/event-hubs-create.md)<br/><br/>[centro de eventos](../articles/event-hubs/event-hubs-create.md)<br/><br/>(Opcional) [regra de autorização](../articles/event-hubs/authorize-access-shared-access-signature.md) para a autenticação baseada em chaves | 
| Ponto final do ônibus de serviço | [Espaço de nome de ônibus de serviço](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Tópico do Service Bus](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Opcional) [regra de autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) para a autenticação baseada em chaves|
