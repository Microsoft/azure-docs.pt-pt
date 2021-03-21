---
title: Implemente um modelo de IA de visão para o seu Azure Percept DK
description: Saiba como implementar um modelo de IA de visão para o seu Azure Percept DK do Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035488"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Implemente um modelo de IA de visão para o seu Azure Percept DK

Siga este guia para implementar um modelo de IA de visão para o seu Azure Percept DK a partir de dentro do Azure Percept Studio.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub

## <a name="model-deployment"></a>Implementação do modelo

1. Energia no seu devkit.

1. Navegue para [o Azure Percept Studio.](https://go.microsoft.com/fwlink/?linkid=2135819)

1. No lado esquerdo da página geral, clique em **Dispositivos**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Ecrã geral do Azure Percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Selecione o seu devkit na lista.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Lista de dispositivos perceptos.":::

1. Na página seguinte, clique em **Implementar um modelo de amostra** se quiser implementar um dos modelos de visão de amostra pré-treinados. Se pretender implementar uma [solução de visão personalizada sem código,](./tutorial-nocode-vision.md)clique em **Implementar um projeto de Visão Personalizada.**

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Escolhas de modelo para implantação.":::

1. Se optou por implementar uma solução de visão sem código, selecione o seu projeto e a iteração de modelos preferidos e clique em **Implementar**.

1. Se optou por implementar um modelo de amostra, selecione o modelo e clique em **Implementar para o dispositivo**.

1. Quando a sua implementação do modelo for bem sucedida, receberá uma mensagem de estado no canto superior direito do ecrã. Para ver o seu modelo inferencing em ação, clique no link **'Ver stream'** na mensagem de estado para ver o fluxo de vídeo RTSP a partir do Vision SoM do seu devkit.

## <a name="next-steps"></a>Passos seguintes

Saiba como ver a sua [telemetria Azure Percept DK](how-to-view-telemetry.md).