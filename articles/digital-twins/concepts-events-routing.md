---
title: Eventos e mensagens de encaminhamento - Azure Digital Twins Microsoft Docs
description: Visão geral de eventos e mensagens de encaminhamento para os pontos finais de serviço com As Gémeas Digitais Azure
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862361"
---
# <a name="routing-iot-events-and-messages"></a>Eventos e mensagens ioT de encaminhamento

As soluções internet of Things muitas vezes unem vários serviços poderosos que incluem armazenamento, análise e muito mais. Este artigo descreve como ligar aplicações azure digital twins a serviços de análise Azure, IA e armazenamento para lhes dar informações e funcionalidades mais profundas.

## <a name="route-types"></a>Tipos de rotas  

A Azure Digital Twins oferece duas formas de ligar eventos IoT a outros serviços Azure ou aplicações empresariais:

* **Eventos de Encaminhamento Azure Digital Twins**: Um objeto no gráfico espacial que muda, dados de telemetria que são recebidos, ou uma função definida pelo utilizador que cria uma notificação baseada em condições pré-definidas pode desencadear eventos Azure Digital Twins. Os utilizadores podem enviar estes eventos para os Hubs de [Eventos Azure,](https://azure.microsoft.com/services/event-hubs/) [tópicos azure service bus](https://azure.microsoft.com/services/service-bus/)ou Rede de [Eventos Azure](https://azure.microsoft.com/services/event-grid/) para posterior processamento.

* **Telemetria do dispositivo de encaminhamento**: Para além de eventos de encaminhamento, as Gémeas Digitais Azure também podem direcionar mensagens de telemetria de dispositivos brutos para Os Hubs de Eventos para mais informações e análises. Este tipo de mensagens não são processadas pela Azure Digital Twins. E só são encaminhados para o centro do evento.

Os utilizadores podem especificar um ou mais pontos finais de saída para enviar eventos ou para enviar mensagens. Os eventos e mensagens serão enviados para os pontos finais de acordo com estas preferências de encaminhamento predefinidos. Por outras palavras, os utilizadores podem especificar um determinado ponto final para receber eventos de operação de gráficos, outro para receber eventos de telemetria do dispositivo, e assim por diante.

[![Eventos de gémeos digitais azure encaminhamento](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

O encaminhamento para os Centros de Eventos mantém a ordem na qual as mensagens de telemetria são enviadas. Então chegam ao ponto final na mesma sequência que foram originalmente recebidos. 

A Rede de Eventos e o Ônibus de Serviço não garantem que os pontos finais receberão eventos da mesma ordem que ocorreram. No entanto, o esquema do evento inclui um carimbo de tempo que pode ser usado para identificar a ordem após os eventos chegarem ao ponto final.

## <a name="route-implementation"></a>Implementação de rotas

O serviço Azure Digital Twins suporta atualmente os seguintes **EndpointTypes:**

* **EventHub** é o ponto final de linha de ligação event Hubs.
* **ServiceBus** é o ponto final da ligação do ônibus de serviço.
* **EventGrid** é o ponto final da linha de ligação da Rede de Eventos.

A Azure Digital Twins suporta atualmente os seguintes **EventoSTypes** que serão enviados para o ponto final escolhido:

* **Os DispositivoS Mensagens** são mensagens de telemetria enviadas a partir dos dispositivos dos utilizadores e reencaminhadas pelo sistema.
* **TopologiaOperação** é uma operação que altera o gráfico ou metadados do gráfico. Um exemplo é adicionar ou apagar uma entidade, como um espaço.
* **SpaceChange** é uma mudança no valor computacional de um espaço que resulta de uma mensagem de telemetria do dispositivo.
* **SensorChange** é uma alteração no valor computacional de um sensor que resulta de uma mensagem de telemetria do dispositivo.
* **UdfCustom** é uma notificação personalizada de uma função definida pelo utilizador.

> [!IMPORTANT]  
> Nem todos os **EndpointTypes** suportam todos os **Tipos de Eventos**.
> Reveja a tabela seguinte para os **Tipos de Eventos** que são permitidos para cada **EndpointType**.

|             | Mensagens de Dispositivo | Operação Topologia | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Para obter mais informações sobre como criar pontos finais e exemplos de esquemas de eventos, leia [Egress e pontos finais.](how-to-egress-endpoints.md)

## <a name="next-steps"></a>Passos seguintes

- Para conhecer os limites de pré-visualização das Gémeas Digitais Azure, leia os limites do serviço de [pré-visualização pública.](concepts-service-limits.md)

- Para experimentar uma amostra de Gémeos Digitais Azure, leia o [quickstart para encontrar quartos disponíveis.](quickstart-view-occupancy-dotnet.md)