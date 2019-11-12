---
title: Comparar a grade de eventos, roteamento para o Hub IoT | Microsoft Docs
description: O Hub IoT oferece seu próprio serviço de roteamento de mensagens, mas também se integra à grade de eventos para publicação de eventos. Compare os dois recursos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906799"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar o roteamento de mensagens e a grade de eventos para o Hub IoT

O Hub IoT do Azure fornece a capacidade de transmitir dados de seus dispositivos conectados e integrá-los aos seus aplicativos de negócios. O Hub IoT oferece dois métodos para integrar eventos de IoT em outros serviços do Azure ou aplicativos de negócios. Este artigo aborda os dois recursos que fornecem esse recurso, para que você possa escolher qual é a melhor opção para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md)** : esse recurso do Hub IOT permite que os usuários direcionem mensagens do dispositivo para a nuvem para pontos de extremidade de serviço como contêineres de armazenamento do Azure, hubs de eventos, filas do barramento de serviço e tópicos do barramento de serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de roteá-los para os pontos de extremidade. Além dos dados de telemetria do dispositivo, você também pode enviar [eventos de não telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para disparar ações. 

**Integração do Hub IOT com a grade de eventos**: a grade de eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. O Hub IoT e a grade de eventos trabalham juntos para [integrar eventos do Hub IOT ao Azure e a serviços não Azure](iot-hub-event-grid.md), quase em tempo real. O Hub IoT publica [eventos de dispositivo](iot-hub-event-grid.md#event-types) e eventos de telemetria.

## <a name="differences"></a>Pequenas

Embora o roteamento de mensagens e a grade de eventos habilitem a configuração de alertas, há algumas diferenças importantes entre os dois. Consulte a tabela a seguir para obter detalhes:

| Funcionalidade | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a grade de eventos |
| ------- | --------------- | ---------- |
| **Mensagens e eventos do dispositivo** | Sim, o roteamento de mensagens pode ser usado para dados de telemetria, alterações no dispositivo de relatório, eventos de ciclo de vida do dispositivo e eventos de alteração de troca digital (parte da [Visualização pública do IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md)). | Sim, a grade de eventos pode ser usada para dados de telemetria, mas também pode relatar quando os dispositivos são criados, excluídos, conectados e desconectados do Hub IoT |
| **Ordenação** | Sim, a ordenação de eventos é mantida.  | Não, a ordem dos eventos não é garantida. | 
| **Aplica** | Filtragem avançada nas propriedades do aplicativo de mensagens, propriedades do sistema de mensagens, corpo da mensagem, marcas de dispositivo e propriedades de dispositivo. A filtragem não é aplicada a eventos de alteração de troca digital. Para obter exemplos, consulte [sintaxe de consulta de roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem com base no tipo de evento, tipo de assunto e atributos em cada evento. Para obter exemplos, consulte [entender os eventos de filtragem em assinaturas da grade de eventos](../event-grid/event-filtering.md). Ao assinar eventos de telemetria, você pode aplicar filtros adicionais aos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o dispositivo de mensagens no Hub IoT, antes de publicar na grade de eventos. Consulte [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Extremidade** | <ul><li>Event Hubs</li> <li>Armazenamento de Blobs do Azure</li> <li>Fila do barramento de serviço</li> <li>Tópicos do Service Bus</li></ul><br>As SKUs do Hub IoT pagas (S1, S2 e S3) são limitadas a 10 pontos de extremidade personalizados. 100 as rotas podem ser criadas por Hub IoT. | <ul><li>Funções do Azure</li> <li>Automatização do Azure</li> <li>Event Hubs</li> <li>Aplicações Lógicas</li> <li>Blob de Armazenamento</li> <li>Tópicos Personalizados</li> <li>Armazenamento de filas</li> <li>Microsoft Flow</li> <li>Serviços de terceiros por meio de WebHooks</li></ul><br>500 pontos de extremidade por Hub IoT têm suporte. Para obter a lista mais atualizada de pontos de extremidade, consulte [manipuladores de eventos de grade de eventos](../event-grid/overview.md#event-handlers). |
| **Custo** | Não há nenhum encargo separado para o roteamento de mensagens. Somente a entrada de telemetria no Hub IoT é cobrada. Por exemplo, se você tiver uma mensagem roteada para três pontos de extremidade diferentes, você será cobrado por apenas uma mensagem. | Não há nenhum encargo do Hub IoT. A grade de eventos oferece as primeiras 100.000 operações por mês gratuitamente e, em seguida, $0.60 por milhão de operações posteriormente. |

## <a name="similarities"></a>Semelhanças

O roteamento de mensagens do Hub IoT e a grade de eventos também têm semelhanças, algumas das quais são detalhadas na tabela a seguir:

| Funcionalidade | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a grade de eventos |
| ------- | --------------- | ---------- |
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para a nuvem | 256 KB, dispositivo para a nuvem |
| **Fiabilidade** | Alta: entrega cada mensagem ao ponto de extremidade pelo menos uma vez para cada rota. Expira todas as mensagens que não são entregues em uma hora. | Alta: entrega cada mensagem ao webhook pelo menos uma vez para cada assinatura. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alta: otimizada para dar suporte a milhões de dispositivos conectados simultaneamente, enviando bilhões de mensagens. | Alta: capaz de rotear eventos 10 milhões por segundo por região. |
| **Latência** | Baixo: quase em tempo real. | Baixo: quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, enviar uma única mensagem para vários pontos de extremidade. | Sim, enviar uma única mensagem para vários pontos de extremidade.  
| **Segurança** | O Hub IOT fornece identidade por dispositivo e controle de acesso revogável. Para obter mais informações, consulte o [controle de acesso do Hub IOT](iot-hub-devguide-security.md). | A grade de eventos fornece validação em três pontos: assinaturas de evento, publicação de eventos e entrega de eventos de webhook. Para obter mais informações, consulte [segurança e autenticação da grade de eventos](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Como escolher

O roteamento de mensagens do Hub IoT e a integração do Hub IoT com a grade de eventos executam ações diferentes para obter resultados semelhantes. Ambos tomam informações de sua solução de Hub IoT e as passam para que outros serviços possam reagir. Então, como você decide qual deles usar? Considere as seguintes perguntas para ajudar a orientar sua decisão: 

* **Que tipo de dados você está enviando para os pontos de extremidade?**

   Use o roteamento de mensagens do Hub IoT quando precisar enviar dados de telemetria para outros serviços. O roteamento de mensagens também permite consultar Propriedades do sistema e do aplicativo de mensagens, corpo da mensagem, marcas de dispositivo e propriedades de dispositivo.

   A integração do Hub IoT com a grade de eventos funciona com eventos que ocorrem no serviço do Hub IoT. Esses eventos do Hub IoT incluem dados de telemetria, dispositivos criados, excluídos, conectados e desconectados. Ao assinar eventos de telemetria, você pode aplicar filtros adicionais aos dados para filtrar as propriedades da mensagem, o corpo da mensagem e o dispositivo de mensagens no Hub IoT, antes de publicar na grade de eventos. Consulte [como filtrar eventos](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Quais pontos de extremidade precisam receber essas informações?**

   O roteamento de mensagens do Hub IoT dá suporte a um número limitado de pontos de extremidade exclusivos e tipos de ponto de extremidades, mas você pode criar conectores para redirecionar os dados e eventos para pontos de extremidades adicionais. Para obter uma lista completa de pontos de extremidade com suporte, consulte a tabela na seção anterior. 

   A integração do Hub IoT com a grade de eventos dá suporte a pontos de extremidade de 500 por Hub IoT e a uma variedade maior de tipos de Endpoint. Ele se integra nativamente com Azure Functions, aplicativos lógicos, armazenamento e filas do barramento de serviço, além de funcionar com WebHooks para estender o envio de dados fora do ecossistema de serviços do Azure e para aplicativos de negócios de terceiros.

* **É importante que seus dados cheguem em ordem?**

   O roteamento de mensagens do Hub IoT mantém a ordem em que as mensagens são enviadas, para que elas cheguem da mesma maneira.

   A grade de eventos não garante que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. Para os casos em que a ordem absoluta de mensagens é significativa e/ou em que um consumidor precisa de um identificador exclusivo confiável para mensagens, é recomendável usar o roteamento de mensagens. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md) e os [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).
* Saiba mais sobre o [Azure Event Grid](../event-grid/overview.md).
* Para saber como criar rotas de mensagens, consulte o tutorial [processar mensagens do dispositivo para a nuvem do Hub IOT usando rotas](../iot-hub/tutorial-routing.md) .
* Experimente a integração da grade de eventos [enviando notificações por email sobre eventos do Hub IOT do Azure usando aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).
