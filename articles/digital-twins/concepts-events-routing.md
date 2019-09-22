---
title: Roteando eventos e mensagens com o Azure digital gêmeos | Microsoft Docs
description: Visão geral de roteamento de eventos e mensagens para pontos de extremidade de serviço com o gêmeos digital do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: alinast
ms.openlocfilehash: 81f2cc32ee10e891ffab127d6ecd7909eb75abd6
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177081"
---
# <a name="routing-events-and-messages"></a>Encaminhar eventos e mensagens

As soluções de IoT muitas vezes abrangem vários serviços poderosos que incluem armazenamento, análise e muito mais. Este artigo descreve como conectar aplicativos do Azure digital gêmeos aos serviços de análise, ia e armazenamento do Azure para fornecer informações e funcionalidades mais aprofundadas.

## <a name="route-types"></a>Tipos de rota  

O Azure digital gêmeos oferece duas maneiras de conectar eventos de IoT com outros serviços do Azure ou aplicativos de negócios:

* **Roteamento de eventos do gêmeos digital do Azure**: Um objeto no grafo espacial que é alterado, os dados de telemetria recebidos ou uma função definida pelo usuário que cria uma notificação com base em condições predefinidas podem disparar eventos gêmeos do Azure digital. Os usuários podem enviar esses eventos para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/), os [Tópicos do barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/)ou a [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para processamento adicional.

* **Telemetria do dispositivo de roteamento**: Além dos eventos de roteamento, o Azure digital gêmeos também pode rotear mensagens brutas de telemetria de dispositivo para os hubs de eventos para obter mais informações e análises. Esses tipos de mensagens não são processados pelo Azure digital gêmeos. E eles são encaminhados apenas para o Hub de eventos.

Os usuários podem especificar um ou mais pontos de extremidade de saída para enviar eventos ou para encaminhar mensagens. Os eventos e as mensagens serão enviados para os pontos de extremidade de acordo com essas preferências de roteamento predefinidas. Em outras palavras, os usuários podem especificar um determinado ponto de extremidade para receber eventos de operação de grafo, outro para receber eventos de telemetria de dispositivo e assim por diante.

[![Roteamento de eventos do gêmeos digital do Azure](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

O roteamento para hubs de eventos mantém a ordem em que as mensagens de telemetria são enviadas. Portanto, eles chegam ao ponto de extremidade na mesma sequência que foram recebidos originalmente. A grade de eventos e o barramento de serviço não garantem que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema de evento inclui um carimbo de data/hora que pode ser usado para identificar a ordem depois que os eventos chegam ao ponto de extremidade.

## <a name="route-implementation"></a>Implementação de rota

O serviço de gêmeos digital do Azure atualmente dá suporte ao seguinte **EndpointTypes**:

* O **EventHub** é o ponto de extremidade da cadeia de conexão dos hubs de eventos.
* **ServiceBus** é o ponto de extremidade de cadeia de conexão do barramento de serviço.
* **EventGrid** é o ponto de extremidade da cadeia de conexão da grade de eventos.

Atualmente, o Azure digital gêmeos dá suporte aos seguintes **EventTypes** que serão enviados para o ponto de extremidade escolhido:

* **DeviceMessages** são mensagens de telemetria enviadas dos dispositivos dos usuários e encaminhadas pelo sistema.
* **TopologyOperation** é uma operação que altera o grafo ou os metadados do grafo. Um exemplo é adicionar ou excluir uma entidade, como um espaço.
* **SpaceChange** é uma alteração no valor calculado de um espaço que resulta de uma mensagem de telemetria do dispositivo.
* **SensorChange** é uma alteração no valor calculado de um sensor que resulta de uma mensagem de telemetria do dispositivo.
* **UdfCustom** é uma notificação personalizada de uma função definida pelo usuário.

> [!IMPORTANT]  
> Nem todos os **EndpointTypes** dão suporte a todos os **EventTypes**.
> Consulte a tabela a seguir para os **EventTypes** que são permitidos para cada **ponto de extremidade**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Para obter mais informações sobre como criar pontos de extremidade e exemplos de esquema de eventos, consulte [saída e pontos de extremidade](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os limites de visualização do Azure digital gêmeos, consulte [limites de serviço de visualização pública](concepts-service-limits.md).

- Para experimentar um exemplo de gêmeos digital do Azure, consulte o guia de [início rápido para localizar as salas disponíveis](quickstart-view-occupancy-dotnet.md).