---
title: Arquitetura de referência para a solução de monitoramento de consumo de água criada com o Azure IoT Central | Microsoft Docs
description: Conheça os conceitos de uma solução de monitoramento de consumo de água criada com o Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 825c6c926f6415e38fdb89517a995a79abb92edd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955341"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Arquitetura de referência de monitoramento de consumo de água 

As soluções de monitoramento de consumo de água podem ser criadas com o **modelo de aplicativo de IOT central do Azure** como um aplicativo de IOT inicial. Este artigo fornece diretrizes de arquitetura de referência de alto nível sobre a criação de uma solução de ponta a ponta. 

![Arquitetura de monitoramento de consumo de água](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Conceitos:

1. Dispositivos e conectividade  
1. IoT Central 
2. Extensibilidade e integrações
3. Aplicações de negócio

Vamos dar uma olhada nos principais componentes que geralmente desempenham uma parte em uma solução de monitoramento de consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Nesta seção, vamos nos referir aos dispositivos usados para soluções de água inteligente, como monitoramento de qualidade de água ou monitoramento de consumo de água, geralmente como dispositivos com água inteligente. Os dispositivos com água inteligente podem ser medidores de fluxo, monitores de qualidade de água, válvulas inteligentes, detectores de vazamento, etc.

Os dispositivos usados em soluções de água inteligente geralmente serão conectados por meio de LPWAN (redes de longa distância) de baixa energia, por meio de um operador de rede de terceiros. Para esses tipos de dispositivos, você pode aproveitar a [ponte de dispositivo IOT central do Azure](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) para enviar os dados do dispositivo para seu aplicativo de IOT no Azure IOT central. Como alternativa, você pode ter gateways de dispositivo que são compatíveis com IP e podem se conectar diretamente a IoT Central.

## <a name="iot-central"></a>IoT Central 
O Azure IoT Central é uma plataforma de aplicativo IoT, que é iniciada e executada rapidamente em sua solução de IoT. Você pode marcar, personalizar e integrar sua solução com serviços de terceiros.
Depois de conectar seus dispositivos de água inteligente ao IoT Central, você obtém o comando e o controle do dispositivo, o monitoramento e o alerta, a interface do usuário com RBAC interno, painéis do insights configuráveis e opções de extensibilidade. 


## <a name="extensibility-and-integrations"></a>Extensibilidade e integrações 
Você pode estender seu aplicativo IoT no IoT Central e, opcionalmente:
* Transforme e integre seus dados de IoT para análise avançada, por exemplo, treinamento de modelos de aprendizado de máquina por meio de exportação de dados contínuas do aplicativo IoT Central
* automatizar fluxos de trabalho em outros sistemas disparando ações por meio de Microsoft Flow ou WebHooks de IoT Central aplicativo
* Acesse seu aplicativo IoT de forma programática em IoT Central por meio de APIs IoT Central

## <a name="business-applications"></a>Aplicações Empresariais 
Os dados de IoT podem ser usados para alimentar uma variedade de aplicativos de negócios em um utilitário de água. Para saber como conectar seu aplicativo de monitoramento de consumo de água IoT Central com serviços de campo, siga o tutorial sobre [como integrar com os serviços de campo do Dynamics 365](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar um consumo de água](./tutorial-water-consumption-monitoring.md) IOT central aplicativo
* Saiba mais sobre os [modelos do governo IOT central](./overview-iot-central-government.md)
* Para saber mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/overview-iot-central)
