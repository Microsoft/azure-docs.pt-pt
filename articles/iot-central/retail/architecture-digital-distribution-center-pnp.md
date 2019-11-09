---
title: Arquitetura IoT Central centro de distribuição digital | Microsoft Docs
description: Uma arquitetura do modelo de aplicativo do centro de distribuição digital para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 478ebde1de5624796ebf1dde5cf89f4e1f9d9104
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890741"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arquitetura do modelo de aplicativo do centro de distribuição digital IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Os parceiros & cliente podem aproveitar o modelo de aplicativo & seguindo as diretrizes para desenvolver soluções de **centro de distribuição digital** de ponta a ponta.

> [!div class="mx-imgBorder"]
> ![do centro de distribuição digital](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
2. Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
3. Os dados são roteados para o serviço do Azure desejado para manipulação
4. Os serviços do Azure, como ASA ou Azure Functions, podem ser aproveitados para reformatar fluxos de dados e enviar para as contas de armazenamento desejadas 
5. Os dados processados são armazenados no armazenamento dinâmico para ações quase em tempo real ou armazenamento frio para aprimoramentos de insights adicionais com base na análise de ML ou em lote. 
6. Os aplicativos lógicos podem ser usados para alimentar vários fluxos de trabalho de negócios em aplicativos de negócios do usuário final

## <a name="details"></a>Detalhes
A seção a seguir descreve cada parte da arquitetura conceitual

## <a name="video-cameras"></a>Câmeras de vídeo 
As câmeras de vídeo são os principais sensores nesse ecossistema de escala empresarial conectada digitalmente. Avanços no aprendizado de máquina e inteligência artificial que permitem que o vídeo seja transformado em dados estruturados e processá-los no Edge antes de enviar para a nuvem. Podemos usar câmeras IP para capturar imagens, compactá-las na câmera e, em seguida, enviar os dados compactados sobre a computação de borda para o pipeline de análise de vídeo ou usar câmeras de Vision de GigE para capturar imagens no sensor e, em seguida, enviar essas imagens diretamente para o Azure IoT Edge , que é compactado antes do processamento no pipeline de análise de vídeo. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge gateway
As "câmeras como sensores" e as cargas de trabalho de borda são gerenciadas localmente por Azure IoT Edge e o fluxo da câmera é processado pelo pipeline de análise. O pipeline de processamento de análise de vídeo em Azure IoT Edge traz inúmeros benefícios, incluindo o tempo de resposta reduzido, o consumo de baixa largura de banda, resultando em baixa latência para processamento rápido de dados. Somente os metadados, as informações ou as ações mais essenciais são enviadas para a nuvem para ação ou investigação posterior. 

## <a name="device-management-with-iot-central"></a>Gerenciamento de dispositivos com IoT Central 
O Azure IoT Central é uma plataforma de desenvolvimento de solução que simplifica o dispositivo IoT & Azure IoT Edge conectividade, configuração e gerenciamento do gateway. A plataforma reduz significativamente a carga e os custos de gerenciamento de dispositivos IoT, operações e desenvolvimentos relacionados. Os clientes & parceiros podem criar soluções empresariais de ponta a ponta para obter um loop de feedback digital nos centros de distribuição.

## <a name="business-insights--actions-via-data-egress"></a>Análises de negócios & ações por meio de egresso de dados 
A plataforma IoT Central fornece opções avançadas de extensibilidade por meio de CDE (exportação de dados contínuas) e APIs. As informações de negócios baseadas no processamento de dados de telemetria ou na telemetria bruta são normalmente exportadas para um aplicativo de linha de negócios preferencial. Isso pode ser obtido por meio de webhook, barramento de serviço, Hub de eventos ou armazenamento de BLOBs para criar, treinar e implantar modelos de aprendizado de máquina & enriquecer ainda mais as ideias.

## <a name="next-steps"></a>Passos seguintes
* Saiba como implantar o [modelo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Saiba mais sobre os [modelos de varejo IOT central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../preview/overview-iot-central.md)
