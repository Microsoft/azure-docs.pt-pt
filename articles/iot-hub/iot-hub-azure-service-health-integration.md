---
title: Verifique o serviço Azure IoT Hub e a saúde dos recursos | Microsoft Docs
description: Use a Azure Service Health e Azure Resource Health para monitorizar o seu IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548581"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Verifique o serviço IoT Hub e a saúde dos recursos

O Azure IoT Hub integra-se com o [serviço de Saúde do Serviço Azure](../service-health/overview.md) para lhe proporcionar a capacidade de monitorizar a saúde ao nível do serviço IoT Hub e dos centros individuais de IoT. Também pode configurar alertas para ser notificado quando o estado do serviço IoT Hub ou um hub IoT mudar. O Serviço de Saúde do Serviço Azure é uma combinação de três serviços menores: Azure Resource Health, Azure Service Health e a página de estado do Azure. As secções deste artigo descrevem cada serviço e a sua relação com o IoT Hub com mais detalhes.

O serviço de saúde do serviço Azure service ajuda-o a monitorizar eventos de nível de serviço, como interrupções e atualizações que podem afetar a disponibilidade do serviço IoT Hub e dos seus centros IoT individuais. O IoT Hub também se integra com o Azure Monitor para fornecer métricas de plataforma IoT Hub e registos de recursos IoT Hub que você pode usar para monitorizar erros e condições operacionais que ocorrem em um centro IoT específico. Para saber mais, consulte [o Monitor IoT Hub.](monitor-iot-hub.md)

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Verifique a saúde de um hub de IoT com Azure Resource Health

A Azure Resource Health faz parte do serviço de saúde do Serviço Azure que acompanha a saúde dos recursos individuais. Pode verificar o estado de saúde do seu hub IoT diretamente a partir do portal.

Para ver o estado e o histórico do seu hub IoT utilizando o portal, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)vá ao seu hub IoT no portal Azure.

1. No painel esquerdo, em **Suporte + resolução de problemas,** selecione **Resource Health**.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Página de saúde de recursos para um hub IoT":::

Para saber mais sobre a Azure Resource Health e como interpretar os dados de saúde, consulte a [visão geral da Saúde dos Recursos](../service-health/resource-health-overview.md) na documentação do Serviço Azure Health.

Também pode selecionar **Adicionar o alerta de saúde dos recursos** para configurar alertas para desencadear quando o estado de saúde do seu hub IoT mudar. Para saber mais, consulte [alertas de configuração para eventos de saúde de serviço](../service-health/alerts-activity-log-service-notifications-portal.md) e tópicos relacionados na documentação do Serviço Azure Health.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Verifique a saúde dos hubs IoT na sua assinatura com a Azure Service Health

Com a Azure Service Health, pode verificar o estado de saúde de todos os centros IoT na sua subscrição.

Para verificar a saúde dos seus centros de IoT, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegar para a saúde do **Recurso de Saúde do Serviço.**  >  

3. A partir das caixas de entrega, selecione a sua subscrição e selecione **O Hub IoT** como o tipo de recurso.

Para saber mais sobre o Serviço Azure Health e como interpretar os dados de saúde, consulte a visão geral do [Serviço de Saúde](../service-health/service-health-overview.md) na documentação do Serviço Azure Health.

Para aprender a configurar alertas com o Serviço Azure Health, consulte [alertas de configuração para eventos de saúde](../service-health/alerts-activity-log-service-notifications-portal.md) de serviço e tópicos relacionados na documentação do Serviço Azure Health.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Verifique a saúde do serviço IoT Hub por região na página do estado de Azure

Para ver o estado do IoT Hub e outros serviços por região em todo o mundo, você pode usar a página de [estado de Azure](https://status.azure.com/status). Para obter mais informações sobre a página do estado de [Azure,](../service-health/azure-status-overview.md) consulte a visão geral do estado de Azure na documentação do Serviço Azure Health.

## <a name="next-steps"></a>Passos seguintes

* Consulte [o serviço de saúde do Serviço Azure](../service-health/overview.md) para obter mais informações sobre a página de Saúde do Serviço Azure, Saúde dos Recursos Azure e página do estado de Azure.
* Consulte [o Monitor Azure IoT Hub](monitor-iot-hub.md) para obter uma descrição da monitorização do Azure IoT Hub.
