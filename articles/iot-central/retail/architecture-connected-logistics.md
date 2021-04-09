---
title: '| logística conectada ioT Microsoft Docs'
description: Uma arquitetura do modelo de aplicação de logística conectada IoT para ioT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d1e17dce80c313bf726451c36ec06dd393549600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832424"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arquitetura do modelo de aplicação logística conectada ioT Central



Parceiros & cliente pode usar o modelo de aplicação & seguindo orientações para desenvolver soluções logísticas terminadas para acabar **com as soluções logísticas conectadas.**

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Conjunto de tags IoT enviando dados de telemetria para um dispositivo de gateway
2. Dispositivos gateway enviando telemetria e insights agregados para a IoT Central
3. Os dados são encaminhados para o serviço Azure desejado para manipulação
4. Serviços Azure como ASA ou Azure Functions podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas
5. Vários fluxos de trabalho de negócios podem ser alimentados por aplicações empresariais de utilizadores finais

## <a name="details"></a>Detalhes
Seguindo a secção descreve cada parte da arquitetura conceptual A ingestão de telemetria de IoT Tags & Gateways

## <a name="iot-tags"></a>Etiquetas IoT
As tags IoT fornecem capacidades físicas, ambientais e ambientais, tais como Temperatura, Humidade, Choque, Inclinação &Luz. As tags IoT normalmente ligam-se ao dispositivo de gateway através de Zigbee (802.15.4). As etiquetas são sensores menos dispendiosos; assim, podem ser descartados no final de uma típica viagem logística para evitar desafios com logística inversa.

## <a name="gateway"></a>Gateway
Gateways também podem atuar como tags IoT com as suas capacidades de deteção ambiente. O gateway permite a conectividade em nuvem Azure IoT (MQTT) a montante utilizando canais celulares Wi-Fi.  Os modos Bluetooth, NFC e 802.15.4 Wireless Sensor Network (WSN) são utilizados para comunicação a jusante com tags IoT. Gateways fornecem fim para acabar com a conectividade segura da nuvem, o emparelhamento de tags IoT, a agregação de dados do sensor, a retenção de dados e a capacidade de configurar limiares de alarme.

## <a name="device-management-with-iot-central"></a>Gestão de dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica a conectividade, configuração e gestão do dispositivo IoT. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com o ioT. Os clientes & parceiros podem construir o fim das soluções empresariais finais para alcançar um ciclo de feedback digital em logística.

## <a name="business-insights-and-actions-using-data-egress"></a>Insights e ações de negócios usando a saída de dados 
A plataforma IoT Central oferece opções de extensibilidade ricas através da Exportação contínua de Dados (CDE) e APIs. Os conhecimentos empresariais baseados no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação preferencial de linha de negócio. Pode ser alcançado usando webhook, service bus, centro de eventos ou armazenamento de bolhas para construir, treinar e implementar modelos de machine learning & enriquecer ainda mais as ideias.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar [o modelo de solução logística conectada](./tutorial-iot-central-connected-logistics.md)
* Saiba mais sobre [os modelos de varejo da IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral do IoT Central](../core/overview-iot-central.md)
