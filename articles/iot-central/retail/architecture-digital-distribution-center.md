---
title: Centro Central de Distribuição Digital IoT IoT [ Centro Central de Distribuição Digital] Microsoft Docs
description: Uma arquitetura do modelo de aplicação do Centro de Distribuição Digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: b73d065b43aff5f9793e642a102ff8bb2a7be036
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000449"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arquitetura do modelo de aplicação do centro de distribuição digital IoT Central



Parceiros e clientes podem usar o modelo de aplicação & seguindo orientações para desenvolver soluções de centro de **distribuição digital** final.

> [!div class="mx-imgBorder"]
> ![centro de distribuição digital](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Conjunto de sensores IoT enviando dados de telemetria para um dispositivo gateway
2. Dispositivos gateway que enviam telemetria e insights agregados para IoT Central
3. Os dados são encaminhados para o serviço Azure pretendido para manipulação
4. Serviços azure como as funções ASA ou Azure podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são armazenados em armazenamento quente para ações quase em tempo real ou armazenamento a frio para melhorias adicionais de insights que se baseiam em ANÁLISE de ML ou lote. 
6. As Aplicações Lógicas podem ser usadas para alimentar vários fluxos de trabalho de negócios em aplicações de negócios de utilizadores finais

## <a name="details"></a>Detalhes
A secção seguinte descreve cada parte da arquitetura conceptual

## <a name="video-cameras"></a>Câmaras de vídeo 
As câmaras de vídeo são os principais sensores neste ecossistema de empresa seletiva conectado digitalmente. Avanços na aprendizagem automática e inteligência artificial que permitem que o vídeo seja transformado em dados estruturados e processá-lo no limite antes de enviar para a nuvem. Podemos usar câmaras IP para capturar imagens, comprimi-las na câmara e, em seguida, enviar os dados comprimidos sobre a computação de borda para o pipeline de análise de vídeo ou usar câmaras de visão GigE para capturar imagens no sensor e, em seguida, enviar estas imagens diretamente para o Azure IoT Edge, que depois comprime antes de processar em pipeline de vídeo. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
As "câmaras-as-sensores" e as cargas de trabalho de borda são geridas localmente pelo Azure IoT Edge e o fluxo de câmaras é processado por pipeline de análise. O pipeline de processamento de análise de vídeo no Azure IoT Edge traz muitos benefícios, incluindo a diminuição do tempo de resposta, o consumo de baixa largura de banda, o que resulta em baixa latência para o processamento rápido de dados. Apenas os metadados, insights ou ações mais essenciais são enviados para a nuvem para mais ações ou investigação. 

## <a name="device-management-with-iot-central"></a>Gestão de Dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica o dispositivo IoT & conectividade, configuração e gestão de gateway Azure IoT Edge. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com dispositivos IoT. Os clientes & parceiros podem construir um fim para acabar com as soluções empresariais para alcançar um ciclo de feedback digital nos centros de distribuição.

## <a name="business-insights-and-actions-using-data-egress"></a>Business Insights e ações usando a saída de dados 
A plataforma Central IoT fornece opções ricas de extebilidade através da Exportação Contínua de Dados (CDE) e APIs. Os conhecimentos empresariais que se baseiam no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação de linha de negócio preferencial. Pode ser alcançado através de webhook, Service Bus, event hub ou armazenamento de blob para construir, treinar e implementar modelos de aprendizagem automática e enriquecer ainda mais insights.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar o modelo do centro de [distribuição digital](./tutorial-iot-central-digital-distribution-center.md)
* Saiba mais sobre os modelos de [retalho IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../core/overview-iot-central.md)
