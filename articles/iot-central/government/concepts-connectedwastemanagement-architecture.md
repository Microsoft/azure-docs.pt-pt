---
title: Arquitetura de referência para solução de gerenciamento de lixo conectada criada com o Azure IoT Central | Microsoft Docs
description: Conheça os conceitos de uma solução de gerenciamento de lixo conectada criada com o Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 806b853b7d3a6291b576c0dde6cdc0b88beec4eb
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027614"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Arquitetura de referência de monitoramento de resíduos conectada 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Uma solução de gerenciamento de lixo conectada pode ser criada usando o **modelo de aplicativo de IOT central do Azure** como um aplicativo de IOT inicial. Este artigo fornece diretrizes de arquitetura de referência de alto nível sobre a criação de uma solução de ponta a ponta. 

![Arquitetura de gerenciamento de resíduos conectada](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Conceitos:

1. Dispositivos e conectividade  
1. IoT Central 
2. Extensibilidade e integrações
3. Aplicações de negócio

Vamos dar uma olhada nos principais componentes que geralmente desempenham uma parte em uma solução de monitoramento de consumo de água.

## <a name="devices-and-connectivity"></a>Dispositivos e conectividade 
Os dispositivos usados em ambientes abertos, como compartimentos de lixo, geralmente podem ser conectados por meio de LPWAN (redes de longa distância) de baixa energia, por meio de um operador de rede de terceiros. Para esses tipos de dispositivos, você pode aproveitar a [ponte de dispositivo IOT central do Azure](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) para enviar os dados do dispositivo para seu aplicativo de IOT no Azure IOT central. Como alternativa, você pode ter gateways de dispositivo que são compatíveis com IP e podem se conectar diretamente a IoT Central.

## <a name="iot-central"></a>IoT Central 
O Azure IoT Central é uma plataforma de aplicativo IoT, que é iniciada e executada rapidamente em sua solução de IoT. Você pode marcar, personalizar e integrar sua solução com serviços de terceiros.
Depois de conectar seus dispositivos Smart aquáticos ao IoT Central, você obtém o comando e o controle, o monitoramento e o alerta do dispositivo, a interface do usuário com o RBAC interno, painéis do insights configuráveis e opções de extensibilidade. 

## <a name="extensibility-and-integrations"></a>Extensibilidade e integrações 
Você pode estender seu aplicativo IoT no IoT Central e, opcionalmente:
* Transforme e integre seus dados de IoT para análise avançada, por exemplo, treinamento de modelos de aprendizado de máquina por meio de exportação de dados contínuas do aplicativo IoT Central. 
* automatizar fluxos de trabalho em outros sistemas disparando ações por meio de Microsoft Flow ou WebHooks de IoT Central aplicativo
* Acesse seu aplicativo IoT de forma programática em IoT Central por meio de APIs IoT Central.

## <a name="business-applications"></a>Aplicações de negócio 
Os dados de IoT podem ser usados para alimentar uma variedade de aplicativos de negócios em um utilitário de desperdício. Para saber como conectar seu IoT Central aplicativo de gerenciamento de lixo conectado a serviços de campo, siga o tutorial sobre [como integrar com os serviços de campo do Dynamics 365](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar um aplicativo de IOT central de gerenciamento de resíduos conectado](./tutorial-connected-waste-management.md)
* Saiba mais sobre os [modelos do governo IOT central](./overview-iot-central-government.md)
* Para saber mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/overview-iot-central)

