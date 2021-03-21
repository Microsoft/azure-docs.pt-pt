---
title: Centro de Distribuição Digital Central IoT arquitetura | Microsoft Docs
description: Uma arquitetura do modelo de aplicação do Centro de Distribuição Digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.openlocfilehash: f5f1a9a51c4aa68c11f1843424186a727be94621
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834311"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arquitetura do modelo de aplicação do centro de distribuição digital IoT Central



Os parceiros e clientes podem usar o modelo de aplicação & seguindo orientações para desenvolver soluções de **centro de distribuição digital.**

> [!div class="mx-imgBorder"]
> ![centro de distribuição digital](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Conjunto de sensores IoT que enviam dados de telemetria para um dispositivo de gateway
2. Dispositivos gateway enviando telemetria e insights agregados para a IoT Central
3. Os dados são encaminhados para o serviço Azure desejado para manipulação
4. Serviços Azure como ASA ou Azure Functions podem ser usados para reformar fluxos de dados e enviar para as contas de armazenamento desejadas
5. Os dados processados são armazenados em armazenamento quente para ações próximas em tempo real ou armazenamento a frio para melhorias adicionais de insights que se baseiam em ML ou análise de lote. 
6. As Aplicações Lógicas podem ser usadas para potenciar vários fluxos de trabalho de negócios em aplicações de negócios de utilizadores finais

## <a name="details"></a>Detalhes
Seguindo a secção descreve cada parte da arquitetura conceptual

## <a name="video-cameras"></a>Câmaras de vídeo 
As câmaras de vídeo são os principais sensores neste ecossistema à escala empresarial ligado digitalmente. Avanços na aprendizagem automática e na inteligência artificial que permitem que o vídeo seja transformado em dados estruturados e processá-lo no limite antes de enviar para a nuvem. Podemos usar câmaras IP para capturar imagens, comprimi-las na câmara e, em seguida, enviar os dados comprimidos sobre o cálculo de borda para o pipeline de análise de vídeo ou usar câmaras de visão GigE para capturar imagens no sensor e, em seguida, enviar estas imagens diretamente para o Azure IoT Edge, que depois comprime antes de processar em pipeline de análise de vídeo. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
As "câmaras-como-sensores" e as cargas de trabalho de borda são geridas localmente pelo Azure IoT Edge e o fluxo de câmara é processado por gasoduto de análise. O pipeline de processamento de análise de vídeo no Azure IoT Edge traz muitos benefícios, incluindo tempo de resposta reduzido, consumo de baixa largura de banda, o que resulta em baixa latência para o processamento rápido de dados. Apenas os metadados, insights ou ações mais essenciais são enviados para a nuvem para mais ações ou investigação. 

## <a name="device-management-with-iot-central"></a>Gestão de Dispositivos com IoT Central 
A Azure IoT Central é uma plataforma de desenvolvimento de soluções que simplifica o dispositivo IoT & conectividade, configuração e gestão do gateway Azure IoT Edge. A plataforma reduz significativamente os encargos e custos da gestão, operações e desenvolvimentos relacionados com o ioT. Os clientes & parceiros podem construir o fim das soluções empresariais finais para alcançar um ciclo de feedback digital nos centros de distribuição.

## <a name="business-insights-and-actions-using-data-egress"></a>Business Insights e ações usando a saída de dados 
A plataforma IoT Central oferece opções de extensibilidade ricas através da Exportação contínua de Dados (CDE) e APIs. Os conhecimentos empresariais baseados no processamento de dados de telemetria ou na telemetria bruta são normalmente exportados para uma aplicação preferencial de linha de negócio. Pode ser alcançado através de webhook, Service Bus, centro de eventos ou armazenamento de bolhas para construir, treinar e implementar modelos de machine learning e enriquecer ainda mais insights.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implementar [o modelo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center.md)
* Saiba mais sobre [os modelos de varejo da IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre ioT Central consulte [a visão geral do IoT Central](../core/overview-iot-central.md)
