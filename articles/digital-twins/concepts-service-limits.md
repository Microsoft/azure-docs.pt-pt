---
title: Limites do serviço de visualização pública – gêmeos digital do Azure | Microsoft Docs
description: Saiba mais sobre os limites do serviço de visualização pública para o gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 3cea4fe65e49bfa2d49822d443103ae6cc6ce69f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014157"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de pré-visualização pública

Durante a visualização pública, o Azure digital gêmeos tem a seguinte assinatura, instância e limites de taxa temporários.

Essas restrições existem para ajudar a simplificar o aprendizado sobre o novo serviço e seus diversos recursos.

> [!NOTE]
> Esses limites serão aumentados ou removidos pela disponibilidade geral (GA).

## <a name="per-subscription-limits"></a>Limites por assinatura

Durante a visualização pública, cada assinatura do Azure pode criar ou executar apenas uma instância de gêmeos digital do Azure por vez.

> [!TIP]
> Se você excluir sua instância, poderá criar uma nova.

## <a name="per-instance-limits"></a>Limites por instância

Por sua vez, cada instância de gêmeos digital do Azure pode ter:

- Exatamente um recurso **IoTHub** inserido que é criado automaticamente durante o provisionamento do serviço.
- Exatamente um ponto de extremidade do **EventHub** para o tipo de evento **DeviceMessage**.
- Até três pontos de extremidade de **EventHub**, **ServiceBus**ou **EventGrid** do tipo de evento **SensorChange**, **SpaceChange**, **TopologyOperation**ou **UdfCustom**.

> [!NOTE]
> Alguns parâmetros que geralmente são definidos na criação das entidades de IoT do Azure acima não são necessários durante a visualização pública.
> - Consulte a [documentação de referência do Swagger](./how-to-use-swagger.md) para obter as especificações mais recentes da API.

## <a name="azure-digital-twins-management-api-limits"></a>Limites da API de gerenciamento de gêmeos digital do Azure

Os limites de taxa de solicitação para sua API de gerenciamento de gêmeos digital do Azure são:

- 100 solicitações por segundo à API de gerenciamento de gêmeos digital do Azure.
- Até 1.000 objetos retornados por uma única consulta da API de gerenciamento de gêmeos digital do Azure.

> [!IMPORTANT]
> Se você exceder o limite de 1.000 objetos, receberá um erro e deverá simplificar sua consulta.

## <a name="user-defined-functions-rate-limits"></a>Limites de taxa de funções definidas pelo usuário

Os limites a seguir definem o número total de todas as chamadas de função definidas pelo usuário feitas para sua instância de gêmeos digital do Azure:

- 400 chamadas de biblioteca de cliente por segundo
- 100 chamadas **SendNotification** por segundo

> [!NOTE]
> As ações a seguir podem fazer com que limites de taxa adicionais sejam aplicados temporariamente:
> - Edições feitas nos metadados do objeto de topologia
> - Atualizações feitas na definição da função definida pelo usuário
> - Dispositivos que enviam telemetria pela primeira vez

## <a name="device-telemetry-limits"></a>Limites de telemetria do dispositivo

Os limites a seguir limitam o número total de todas as mensagens que seus dispositivos podem enviar para sua instância de gêmeos digital do Azure:

- 100 mensagens por segundo em todos os dispositivos
-   25 mensagens por segundo por dispositivo

## <a name="next-steps"></a>Passos seguintes

- Para experimentar um exemplo de gêmeos digital do Azure, vá para [início rápido para encontrar as salas disponíveis](./quickstart-view-occupancy-dotnet.md).
