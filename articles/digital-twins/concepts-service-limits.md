---
title: Limites de serviço de pré-visualização pública - Azure Digital Twins Microsoft Docs
description: Conheça o serviço público de pré-visualização, subscrição, instância e limites de tarifas para As Gémeas Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370389"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de pré-visualização pública

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Durante a pré-visualização pública, a Azure Digital Twins tem os seguintes limites temporários de subscrição, instância e tarifas para os clientes existentes. Estes constrangimentos existem para ajudar a simplificar a aprendizagem sobre o novo serviço e as suas muitas funcionalidades, e serão aumentados ou removidos pela disponibilidade geral (GA).

## <a name="per-subscription-limits"></a>Limites por subscrição

Durante a pré-visualização pública, cada subscrição do Azure pode criar ou executar apenas uma instância de Gémeos Digitais Azure de cada vez. Se apagar a sua instância, pode criar uma nova.

## <a name="per-instance-limits"></a>Limites por instância

Por sua vez, cada instância de Gémeos Digitais Azure pode ter:

- Exatamente um recurso **IoTHub** incorporado que é criado automaticamente durante o fornecimento de serviços.
- Exatamente um ponto final **do EventHub** para o tipo de evento **DeviceMessage**.
- Até três Pontos finais **EventHub,** **ServiceBus,** ou **EventGrid** do tipo evento **SensorChange,** **SpaceChange,** **TopologyOperation**ou **UdfCustom**.

> [!NOTE]
> Alguns parâmetros que são geralmente definidos na criação das entidades azure IoT acima não são necessários durante a pré-visualização pública.
> - Consulte a [documentação](./how-to-use-swagger.md) de referência swagger para as especificações mais recentes da API.

## <a name="azure-digital-twins-management-api-limits"></a>Limites da API de Gestão de Gémeos Digitais Azure

Os limites de taxa de pedido para a sua API de Gestão de Gémeos Digitais Azure são:

- 100 pedidos por segundo à API de Gestão de Gémeos Digitais Azure.
- Até 1.000 objetos devolvidos por uma única consulta da API de Gestão de Gémeos Digitais Azure.

> [!IMPORTANT]
> Se exceder o limite de 1.000 objetos, receberá um erro e deve simplificar a sua consulta.

## <a name="user-defined-functions-rate-limits"></a>Limites de taxa de taxa de funções definidas pelo utilizador

Os seguintes limites definem o número total de todas as chamadas de função definidas pelo utilizador feitas para a sua instância Azure Digital Twins:

- 400 chamadas de biblioteca de clientes por segundo
- 100 Chamadas **de Notificação de Envio** por segundo

> [!NOTE]
> As seguintes ações podem fazer com que os limites adicionais das taxas sejam aplicados temporariamente:
> - Edificas feitas para os metadados de objetos de topo
> - Atualizações feitas à definição de função definida pelo utilizador
> - Dispositivos que enviam telemetria pela primeira vez

## <a name="device-telemetry-limits"></a>Limites de telemetria do dispositivo

Os seguintes limites cobrem o número total de todas as mensagens que os seus dispositivos podem enviar para a sua instância Azure Digital Twins:

- 100 mensagens por segundo em todos os dispositivos
-    25 mensagens por segundo por dispositivo

## <a name="next-steps"></a>Passos seguintes

- Para experimentar uma amostra de Gémeos Digitais Azure, vá ao [Quickstart para encontrar quartos disponíveis.](./quickstart-view-occupancy-dotnet.md)
