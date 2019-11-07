---
title: Logística da arquitetura conectada por IoT | Microsoft Docs
description: Uma arquitetura de modelo de aplicativo de logística conectada a IoT para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 1940f02fdf9badcf99ef85a2ab02ac9099cc1775
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615384"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arquitetura do modelo de aplicativo de logística conectado IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Os parceiros & cliente podem aproveitar o modelo de aplicativo & seguindo as diretrizes para desenvolver **soluções de logística conectadas**de ponta a ponta.

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Conjunto de marcas IoT enviando dados de telemetria para um dispositivo de gateway
2. Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
3. Os dados são roteados para o serviço do Azure desejado para manipulação
4. Os serviços do Azure, como ASA ou Azure Functions, podem ser aproveitados para reformatar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Vários fluxos de trabalho de negócios podem ser alimentados por aplicativos de negócios de usuário final

## <a name="details"></a>Detalhes
A seção a seguir descreve cada parte da ingestão de telemetria da arquitetura conceitual de marcas IoT & gateways

## <a name="iot-tags"></a>Marcas IoT
As marcas de IoT fornecem recursos de sensor físico, ambiente e ambiental, como temperatura, umidade, choque, inclinação & luz. As marcas IoT normalmente se conectam ao dispositivo de gateway via ZigBee (802.15.4). As marcas são sensores de baixo custo; Portanto, eles podem ser descartados no final de uma jornada de logística típica para evitar desafios com logística reversa.

## <a name="gateway"></a>Gateway
Os gateways também podem atuar como marcas IoT com seus recursos de detecção de ambiente. O gateway habilita o upstream MQTT (conectividade de nuvem IoT) do Azure por meio de canais celulares e Wi-Fi.  Os modos de WSN (rede de sensor sem fio) Bluetooth, NFC e 802.15.4 são usados para comunicação downstream com marcas IoT. Os gateways fornecem conectividade de nuvem segura de ponta a ponta, emparelhamento de marca IoT, agregação de dados de sensor, retenção de dados e capacidade de configurar limites de alarme.

## <a name="device-management-with-iot-central"></a>Gerenciamento de dispositivos com IoT Central 
O Azure IoT Central é uma plataforma de desenvolvimento de solução que simplifica a conectividade, a configuração e o gerenciamento do dispositivo IoT. A plataforma reduz significativamente a carga e os custos de gerenciamento de dispositivos IoT, operações e desenvolvimentos relacionados. Os clientes & parceiros podem criar soluções empresariais de ponta a ponta para alcançar um loop de feedback digital na logística.

## <a name="business-insights--actions-via-data-egress"></a>Análises de negócios & ações por meio de egresso de dados 
A plataforma IoT Central fornece opções avançadas de extensibilidade por meio de CDE (exportação de dados contínuas) e APIs. As informações de negócios baseadas no processamento de dados de telemetria ou na telemetria bruta são normalmente exportadas para um aplicativo de linha de negócios preferencial. Isso pode ser obtido por meio de webhook, barramento de serviço, Hub de eventos ou armazenamento de BLOBs para criar, treinar e implantar modelos de aprendizado de máquina & enriquecer ainda mais as ideias.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implantar o [modelo de solução de logística conectada](./tutorial-iot-central-connected-logistics-pnp.md)
* Saiba mais sobre os [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../core/overview-iot-central-pnp.md)
