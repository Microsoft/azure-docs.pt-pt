---
title: Encaminhamento de eventos e mensagens duplos Digital do Azure | Documentos da Microsoft
description: Descrição geral do encaminhamento de eventos e mensagens para pontos finais de serviço duplos Digital do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 7dfda00aca403f7f95f0c56d1db28c3c609bebd2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080662"
---
# <a name="routing-events-and-messages"></a>Encaminhar eventos e mensagens

Soluções de IoT unam-se, muitas vezes, vários serviços poderosos que incluem armazenamento, análise e muito mais. Este artigo descreve como ligar aplicações de duplos Digital do Azure aos serviços de análise, IA e armazenamento do Azure para lhes dar mais informações e funcionalidades.

## <a name="route-types"></a>Tipos de rota  

Os gémeos Digital do Azure oferece duas formas de ligar eventos de IoT com outros serviços do Azure ou aplicativos de negócios:

* **Eventos de roteamento duplos Digital do Azure**: Um objeto a geográficos no gráfico que são alterados, os dados telemétricos recebidos, ou uma função definida pelo utilizador que cria uma notificação com base nas condições predefinidas pode acionar eventos de duplos Digital do Azure. Os utilizadores podem enviar estes eventos para [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [tópicos do Service bus do Azure](https://azure.microsoft.com/services/service-bus/), ou [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para processamento adicional.

* **Telemetria do dispositivo de encaminhamento**: Além de eventos de roteamento, duplos Digital do Azure pode também encaminhar mensagens de telemetria do dispositivo sem formato para os Hubs de eventos para obter mais informações e análises. Esses tipos de mensagens não são processados por duplos Digital do Azure. E eles apenas são reencaminhados para o hub de eventos.

Os utilizadores podem especificar um ou mais pontos finais de saída para enviar eventos ou para reencaminhar mensagens. Eventos e de mensagens serão enviadas para os pontos finais de acordo com essas preferências de encaminhamento predefinidos. Em outras palavras, os utilizadores podem especificar um determinado ponto final para receber eventos da operação de gráfico, outro para receber eventos de telemetria do dispositivo e assim por diante.

![Eventos de duplos Digital do Azure, encaminhamento][1]

Encaminhamento para os Hubs de eventos mantém a ordem na qual são enviadas mensagens de telemetria. Assim que chegarem no ponto final na mesma sequência como foram originalmente recebidos. Grelha de eventos e o Service Bus não garantem que os pontos finais vão receber eventos na mesma ordem em que ocorreu. No entanto, o esquema de eventos inclui um carimbo que pode ser utilizado para identificar a ordem após os eventos deparar-se com o ponto final.

## <a name="route-implementation"></a>Implementação de rota

O serviço de duplos Digital do Azure suporta atualmente o seguinte procedimento **EndpointTypes**:

* **EventHub** é o ponto de final de cadeia de ligação dos Hubs de eventos.
* **ServiceBus** é o ponto de final de cadeia de ligação do Service Bus.
* **EventGrid** é o ponto de final de cadeia de ligação Event Grid.

Atualmente, o duplos Digital do Azure suporta o seguinte **EventTypes** que serão enviadas para o ponto de final escolhido:

* **DeviceMessages** são mensagens de telemetria enviadas a partir de dispositivos dos utilizadores e reencaminhadas pelo sistema.
* **TopologyOperation** é uma operação que altera o gráfico ou metadados do gráfico. Um exemplo é adicionar ou excluir uma entidade, como um espaço.
* **SpaceChange** é uma alteração no valor calculado um espaço que resulta de uma mensagem de telemetria do dispositivo.
* **SensorChange** é uma alteração no valor calculado um sensor que resulta de uma mensagem de telemetria do dispositivo.
* **UdfCustom** é uma notificação de uma função definida pelo utilizador.

> [!IMPORTANT]  
> Nem todos os **EndpointTypes** suportam todos **EventTypes**.
> Consulte a tabela seguinte para o **EventTypes** que têm permissão para cada **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Para obter mais informações sobre como criar pontos de extremidade e exemplos de esquema dos eventos, consulte [saída e os pontos finais](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Passos Seguintes

- Para saber quais duplos Digital do Azure em limites de pré-visualização, veja [limites de serviço de pré-visualização pública](concepts-service-limits.md).

- Para experimentar um exemplo de duplos Digital do Azure, consulte a [guia de introdução de salas disponíveis](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
