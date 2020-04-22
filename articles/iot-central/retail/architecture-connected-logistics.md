---
title: Arquitetura IoT Logística conectada [ Logística conectada ] Microsoft Docs
description: Uma arquitetura do modelo de aplicação logística conectada IoT para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686233"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arquitetura do modelo de aplicação logística ligada ioT Central



Parceiros & cliente pode usar o modelo de aplicação & seguindo orientações para desenvolver **soluções logísticas conectadas**de ponta a ponta.

> [!div class="mx-imgBorder"]
> ![painel logístico conectado](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Conjunto de tags IoT enviando dados de telemetria para um dispositivo gateway
2. Dispositivos gateway que enviam telemetria e insights agregados para IoT Central
3. Os dados são encaminhados para o serviço Azure pretendido para manipulação
4. Serviços azure como as funções ASA ou Azure podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Vários fluxos de trabalho empresariais podem ser alimentados por aplicações de negócio sinuoso

## <a name="details"></a>Detalhes
A secção seguinte descreve cada parte da arquitetura conceptual A ingestão de Telemetria de IoT Tags & Gateways

## <a name="iot-tags"></a>Etiquetas ioT
As etiquetas ioT fornecem capacidades físicas, ambientais e de sensores ambientais tais como Temperatura, Humidade, Choque, Inclinação &Luz. As etiquetas IoT normalmente ligam-se ao dispositivo gateway através de Zigbee (802.15.4). As etiquetas são sensores mais baratos; assim, podem ser descartados no final de uma típica viagem logística para evitar desafios com logística inversa.

## <a name="gateway"></a>Gateway
Gateways também podem atuar como tags IoT com as suas capacidades de deteção ambiente. O portal permite a conectividade em nuvem Azure IoT (MQTT) utilizando canais celulares e Wi-Fi.  Os modos Bluetooth, NFC e 802.15.4 Wireless Sensor Network (WSN) são utilizados para a comunicação a jusante com tags IoT. Os gateways fornecem conectividade de ponta a nuvem segura, emparelhamento de etiquetas IoT, agregação de dados de sensores, retenção de dados e capacidade de configurar limiares de alarme.

## <a name="device-management-with-iot-central"></a>Gestão de dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica a conectividade, configuração e gestão do dispositivo IoT. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com dispositivos IoT. Os clientes & parceiros podem construir um fim para acabar com as soluções empresariais para alcançar um ciclo de feedback digital na logística.

## <a name="business-insights-and-actions-using-data-egress"></a>Insights e ações de negócio suster dados 
A plataforma Central IoT fornece opções ricas de extebilidade através da Exportação Contínua de Dados (CDE) e APIs. Os conhecimentos empresariais baseados no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação de linha de negócio preferida. Pode ser alcançado usando webhook, ônibus de serviço, hub de eventos ou armazenamento de blob para construir, treinar e implementar modelos de aprendizagem automática & enriquecer ainda mais insights.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar [modelo de solução logística conectada](./tutorial-iot-central-connected-logistics.md)
* Saiba mais sobre os modelos de [retalho IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../core/overview-iot-central.md)
