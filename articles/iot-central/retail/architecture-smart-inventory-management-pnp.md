---
title: Gerenciamento de inventário inteligente de IoT de arquitetura | Microsoft Docs
description: Uma arquitetura de modelo de gerenciamento de inventário inteligente do IoT para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 6450169ae2b2d74006eedc66f35338494257594a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889093"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arquitetura do modelo de aplicativo de gerenciamento de inventário inteligente do IoT Central

Os parceiros & cliente podem aproveitar o modelo de aplicativo & seguindo as diretrizes para desenvolver soluções de **Gerenciamento de inventário inteligente** de ponta a ponta.

> [!div class="mx-imgBorder"]
> ](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png) de gerenciamento de inventário inteligente ![

1. Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
2. Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
3. Os dados são roteados para o serviço do Azure desejado para manipulação
4. Os serviços do Azure, como ASA ou Azure Functions, podem ser aproveitados para reformatar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são armazenados no armazenamento dinâmico para ações quase em tempo real ou armazenamento frio para aprimoramentos de insights adicionais com base na análise de ML ou em lote. 
6. Os aplicativos lógicos podem ser usados para alimentar vários fluxos de trabalho de negócios em aplicativos de negócios do usuário final

## <a name="details"></a>Detalhes
A seção a seguir descreve cada parte da ingestão de telemetria da arquitetura conceitual de RFID (identificação de frequência de rádio), marcas de BLE (Bluetooth de baixa energia)

## <a name="rfid-tags"></a>Marcas de RFID
As marcas RFID transmitem dados sobre um item por meio de ondas de rádio. As marcas de RFID normalmente não têm uma bateria, a menos que especificado. As marcas recebem energia das ondas de rádio geradas pelo leitor e transmitem um sinal de volta para o leitor de RFID.

## <a name="ble-tags"></a>Marcas de BLE
O Beacon de energia transmite pacotes de dados em intervalos regulares. Os dados de Beacon são detectados por leitores do BLE ou serviços instalados em smartphones e, em seguida, transmitindo-os para a nuvem.

## <a name="rfid--ble-readers"></a>& De RFID para leitores de BLE
O leitor de RFID converte as ondas de rádio em uma forma mais utilizável de dados. As informações coletadas das marcas são então armazenadas no servidor de borda local ou enviadas para a nuvem via JSON-RPC 2,0 em MQTT.
O leitor de BLE também conhecido como pontos de acesso (AP) é semelhante ao leitor de RFID. Eles são usados para detectar sinais Bluetooth próximos e retransmitir sua mensagem para Azure IoT Edge local ou nuvem via JSON-RPC 2,0 em MQTT.
Muitos leitores são capazes de ler sinais de Beacon de & RFID, bem como fornecer recursos adicionais de sensor relacionados a temperatura, umidade, acelerômetro & giro.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge gateway
O Azure IoT Edge Server fornece um local para pré-processar esses dados localmente antes de enviá-los para a nuvem. Também podemos implantar cargas de trabalho de nuvem de inteligência artificial, serviços do Azure e de terceiros, lógica de negócios por meio de contêineres padrão.

## <a name="device-management-with-iot-central"></a>Gerenciamento de dispositivos com IoT Central 
O Azure IoT Central é uma plataforma de desenvolvimento de solução que simplifica a conectividade, a configuração e o gerenciamento do dispositivo IoT. A plataforma reduz significativamente a carga e os custos de gerenciamento de dispositivos IoT, operações e desenvolvimentos relacionados. Os clientes & parceiros podem criar soluções empresariais de ponta a ponta para obter um loop de feedback digital no gerenciamento de estoque.

## <a name="business-insights--actions-via-data-egress"></a>Análises de negócios & ações por meio de egresso de dados 
A plataforma IoT Central fornece opções avançadas de extensibilidade por meio de CDE (exportação de dados contínuas) e APIs. As informações de negócios baseadas no processamento de dados de telemetria ou na telemetria bruta são normalmente exportadas para um aplicativo de linha de negócios preferencial. Isso pode ser obtido por meio de webhook, barramento de serviço, Hub de eventos ou armazenamento de BLOBs para criar, treinar e implantar modelos de aprendizado de máquina & enriquecer ainda mais as ideias.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implantar o [modelo de gerenciamento de inventário inteligente](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Saiba mais sobre os [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../preview/overview-iot-central.md)
