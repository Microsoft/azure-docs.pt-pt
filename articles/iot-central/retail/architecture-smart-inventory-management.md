---
title: '| de Gestão de Inventário Inteligente IoT de Arquitetura Microsoft Docs'
description: Uma arquitetura do modelo de gestão de inventário inteligente IoT para ioT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832645"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arquitetura do modelo de aplicação de gestão de inventário inteligente IoT Central

Os parceiros e clientes podem usar o modelo de aplicação e seguir orientações para desenvolver soluções **de gestão de inventário inteligentes.**

> [!div class="mx-imgBorder"]
> ![gestão inteligente de inventário](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Conjunto de sensores IoT que enviam dados de telemetria para um dispositivo de gateway
2. Dispositivos gateway enviando telemetria e insights agregados para a IoT Central
3. Os dados são encaminhados para o serviço Azure desejado para manipulação
4. Serviços Azure como ASA ou Azure Functions podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são armazenados em armazenamento quente para ações próximas em tempo real ou armazenamento a frio para melhorias adicionais de insights que se baseiam em ML ou análise de lote. 
6. As Aplicações Lógicas podem ser usadas para potenciar vários fluxos de trabalho de negócios em aplicações de negócios de utilizadores finais

## <a name="details"></a>Detalhes
Seguindo a secção descreve cada parte da arquitetura conceptual A ingestão de telemetria a partir da identificação de radiofrequência (RFID), tags de baixa energia Bluetooth (BLE)

## <a name="rfid-tags"></a>Etiquetas RFID
As tags RFID transmitem dados sobre um item através de ondas de rádio. As etiquetas RFID normalmente não têm uma bateria a menos que seja especificada. As etiquetas recebem energia das ondas de rádio geradas pelo leitor e transmitem um sinal de volta para o leitor RFID.

## <a name="ble-tags"></a>Etiquetas BLE
O farol de energia transmite pacotes de dados a intervalos regulares. Os dados do farol são detetados pelos leitores ble ou serviços instalados em smartphones e, em seguida, transmitindo-os para a nuvem.

## <a name="rfid--ble-readers"></a>Leitores RFID & BLE
O leitor RFID converte as ondas de rádio numa forma de dados mais utilizável. As informações recolhidas nas tags são então armazenadas no servidor de borda local ou enviadas para a nuvem utilizando json-RPC 2.0 sobre MQTT.
O leitor BLE também conhecido como Pontos de Acesso (AP) é semelhante ao leitor RFID. É utilizado para detetar sinais Bluetooth próximos e transmitir a sua mensagem para a borda ou nuvem local Azure IoT Edge ou nuvem usando JSON-RPC 2.0 sobre MQTT.
Muitos leitores são capazes de ler sinais RFID e beacon, e fornecer capacidade de sensor adicional relacionada com temperatura, humidade, acelerómetro e giroscópio.

## <a name="azure-iot-edge-gateway"></a>Gateway Azure IoT Edge
O servidor Azure IoT Edge fornece um local para pré-processar esses dados localmente antes de enviá-los para a nuvem. Também podemos implementar cargas de trabalho em nuvem inteligência artificial, Azure e serviços de terceiros, lógica de negócio usando recipientes padrão.

## <a name="device-management-with-iot-central"></a>Gestão de dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica a conectividade, configuração e gestão do dispositivo IoT. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com o ioT. Os clientes & parceiros podem construir o fim das soluções empresariais finais para alcançar um ciclo de feedback digital na gestão de inventários.

## <a name="business-insights--actions-using-data-egress"></a>Insights empresariais & ações usando a saída de dados 
A plataforma IoT Central oferece opções de extensibilidade ricas através da Exportação contínua de Dados (CDE) e APIs. Os conhecimentos empresariais baseados no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação preferencial de linha de negócio. Pode ser alcançado usando webhook, service bus, centro de eventos ou armazenamento de bolhas para construir, treinar e implementar modelos de machine learning & enriquecer ainda mais as ideias.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar o modelo inteligente de [gestão de inventário](./tutorial-iot-central-smart-inventory-management.md)
* Saiba mais sobre [os modelos de varejo da IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral do IoT Central](../core/overview-iot-central.md)
