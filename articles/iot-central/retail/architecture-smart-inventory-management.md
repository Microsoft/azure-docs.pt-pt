---
title: Gestão de Inventário Inteligente IoT de Arquitetura IoT [ Microsoft Docs
description: Uma arquitetura do modelo de gestão de inventário inteligente IoT para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 9d52b2fa9289da709449a5f1edc527239800dfa1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000645"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arquitetura do modelo de aplicação de gestão de inventário inteligente IoT Central

Parceiros e clientes podem usar o modelo de app e seguir orientações para desenvolver soluções de gestão de **inventário inteligente.**

> [!div class="mx-imgBorder"]
> ![gestão inteligente de inventário](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Conjunto de sensores IoT enviando dados de telemetria para um dispositivo gateway
2. Dispositivos gateway que enviam telemetria e insights agregados para IoT Central
3. Os dados são encaminhados para o serviço Azure pretendido para manipulação
4. Serviços azure como as funções ASA ou Azure podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são armazenados em armazenamento quente para ações quase em tempo real ou armazenamento a frio para melhorias adicionais de insights que se baseiam em ANÁLISE de ML ou lote. 
6. As Aplicações Lógicas podem ser usadas para alimentar vários fluxos de trabalho de negócios em aplicações de negócios de utilizadores finais

## <a name="details"></a>Detalhes
A secção seguinte descreve cada parte da arquitetura conceptual A ingestão de telemetria a partir da identificação de radiofrequência (RFID), etiquetas de baixa energia Bluetooth (BLE)

## <a name="rfid-tags"></a>Tags RFID
As etiquetas RFID transmitem dados sobre um item através de ondas de rádio. As etiquetas RFID normalmente não têm uma bateria a menos que especificadas. As etiquetas recebem energia das ondas de rádio geradas pelo leitor e transmitem um sinal de volta para o leitor RFID.

## <a name="ble-tags"></a>Tags BLE
O farol de energia transmite pacotes de dados em intervalos regulares. Os dados do farol são detetados por leitores BLE ou serviços instalados em smartphones e, em seguida, transmitindo-os para a nuvem.

## <a name="rfid--ble-readers"></a>RFID & leitores BLE
O leitor RFID converte as ondas de rádio para uma forma de dados mais utilizável. As informações recolhidas das etiquetas são então armazenadas no servidor de borda local ou enviadas para a nuvem utilizando jSON-RPC 2.0 sobre MQTT.
O leitor BLE também conhecido como Access Points (AP) é semelhante ao leitor RFID. É usado para detetar sinais Bluetooth próximos e transmitir a sua mensagem para azure local IoT Edge ou nuvem usando JSON-RPC 2.0 sobre MQTT.
Muitos leitores são capazes de ler sinais RFID e farol, e fornecer capacidade adicional de sensor relacionado com temperatura, humidade, acelerómetro e giroscópio.

## <a name="azure-iot-edge-gateway"></a>Gateway Azure IoT Edge
O servidor Azure IoT Edge fornece um local para pré-processar esses dados localmente antes de enviá-los para a nuvem. Também podemos implementar cargas de trabalho em nuvem inteligência artificial, serviços Azure e serviços de terceiros, lógica empresarial usando recipientes padrão.

## <a name="device-management-with-iot-central"></a>Gestão de dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica a conectividade, configuração e gestão do dispositivo IoT. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com dispositivos IoT. Os clientes & parceiros podem construir um fim para acabar com as soluções empresariais para alcançar um ciclo de feedback digital na gestão de inventários.

## <a name="business-insights--actions-using-data-egress"></a>Insights de negócio& ações usando a saída de dados 
A plataforma Central IoT fornece opções ricas de extebilidade através da Exportação Contínua de Dados (CDE) e APIs. Os conhecimentos empresariais baseados no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação de linha de negócio preferida. Pode ser alcançado usando webhook, ônibus de serviço, hub de eventos ou armazenamento de blob para construir, treinar e implementar modelos de aprendizagem automática & enriquecer ainda mais insights.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar modelo inteligente de [gestão](./tutorial-iot-central-smart-inventory-management.md) de inventário
* Saiba mais sobre os modelos de [retalho IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../core/overview-iot-central.md)
