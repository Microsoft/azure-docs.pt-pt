---
title: Modelos Azure Percept AI
description: Saiba mais sobre os modelos de IA disponíveis para prototipagem e implantação
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e31a696dfb443c20566f13f5a228eefac7af5ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595928"
---
# <a name="azure-percept-ai-models"></a>Modelos Azure Percept AI

O Azure Percept permite-lhe desenvolver e implementar modelos de IA diretamente para o seu Azure Percept DK do [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). A implementação do modelo utiliza [o Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) e [o Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Modelos de AI de amostra

O Azure Percept Studio contém modelos de amostra para as seguintes aplicações:

- deteção de pessoas
- deteção de veículos
- deteção geral de objetos
- deteção de produtos na prateleira

Com modelos pré-treinados, não é necessária codificação ou recolha de dados de formação. Basta implementar o modelo pretendido para o seu Azure Percept DK a partir do portal e abrir o fluxo de vídeo do seu devkit para ver o modelo inferencing em ação. A telemetria de inferenculação do modelo também pode ser acedida através da ferramenta [Azure IoT Explorer.](https://github.com/Azure/azure-iot-explorer/releases)

## <a name="reference-solutions"></a>Soluções de referência

Uma [solução de referência de contagem de pessoas](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) também está disponível. Esta solução de referência é uma aplicação de IA de código aberto que fornece pessoas baseadas em bordas contando com eventos de entrada/saída de zona definidos pelo utilizador. A saída de vídeo e IA do dispositivo de borda no local é saídada para [OZure Data Lake,](https://azure.microsoft.com/solutions/data-lake/)com a interface do utilizador a funcionar como um website Azure. A inferencing da IA é fornecida por um modelo de IA de código aberto para deteção de pessoas.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Analítica espacial solução pré-construída gif.":::

## <a name="custom-no-code-solutions"></a>Soluções personalizadas sem código

Através do Azure Percept Studio, pode desenvolver soluções personalizadas [de visão](./tutorial-nocode-vision.md) e fala, sem necessidade de codificação.

Para soluções de visão personalizada, estão disponíveis modelos de IA de deteção e classificação de objetos. Basta carregar e marcar as suas imagens de treino, que podem ser tiradas diretamente com o Azure Percept Vision SoM do Azure Percept DK, se desejar. A formação e avaliação de modelos são facilmente realizadas na [Visão Personalizada,](https://www.customvision.ai/)que faz parte dos [Serviços Cognitivos Azure.](https://azure.microsoft.com/services/cognitive-services/#overview)

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

Para soluções de fala personalizadas, os modelos de assistente de voz estão atualmente disponíveis para as seguintes aplicações:

- Hospitalidade: quarto de hotel equipado com dispositivos inteligentes controlados por voz.
- Cuidados de saúde: unidade de cuidados equipados com dispositivos inteligentes controlados por voz.
- Inventário: centro de inventário equipado com dispositivos inteligentes controlados por voz.
- Automotive: hub automóvel equipado com dispositivos inteligentes controlados por voz.

Palavras-chave e comandos pré-construídos do assistente de voz estão disponíveis diretamente através do portal. Palavras-chave e comandos personalizados podem ser criados e treinados no [Speech Studio,](https://speech.microsoft.com/)que também faz parte dos Serviços Cognitivos do Azure.

## <a name="advanced-development"></a>Desenvolvimento avançado

Consulte o [Azure Percept DK desenvolvimento avançado GitHub](https://github.com/microsoft/azure-percept-advanced-development) para orientação atualizada, tutoriais e exemplos para coisas como:

* Trazer um modelo de IA personalizado para o dispositivo
* Atualizar um modelo que já apoiamos com a aprendizagem de transferências
* E mais
