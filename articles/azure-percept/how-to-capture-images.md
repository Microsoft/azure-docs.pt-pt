---
title: Capture imagens para uma solução de visão sem código no Azure Percept Studio
description: Saiba como capturar imagens com o seu Azure Percept DK no Azure Percept Studio para uma solução de visão sem código
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 44bf498af52f4d8a0d880dc1f1d5874d5b444cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035539"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Captar imagens para um projeto de visão no Azure Percept Studio

Siga este guia para capturar imagens usando o Vision SoM do Azure Percept DK para um projeto de visão existente no Azure Percept Studio. Se ainda não criou um projeto de visão, consulte o [tutorial de visão sem código.](./tutorial-nocode-vision.md)

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub
- [Projeto de visão sem código](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Captar imagens

1. Energia no seu devkit.

1. Navegue para [o Azure Percept Studio.](https://go.microsoft.com/fwlink/?linkid=2135819)

1. No lado esquerdo da página geral, clique em **Dispositivos**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Ecrã geral do Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Selecione o seu devkit na lista.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Lista de dispositivos perceptos.":::

1. Na página do seu dispositivo, clique em **Capturar imagens para um projeto**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Página de dispositivos perceptos com ações disponíveis listadas.":::

1. Na janela de captura de **imagem,** faça o seguinte:

    1. No menu Desistência do **Projeto,** selecione o projeto de visão para o quais pretende recolher imagens.

    1. Clique **em Ver o fluxo do dispositivo** para garantir que a câmara do Vision SoM está corretamente colocada.

    1. Clique **em Tirar a foto** para capturar uma imagem.

    1. Em alternativa, verifique a caixa ao lado **da captura automática de imagens** para configurar um temporizador para a captura de imagens:

        1. Selecione a sua taxa de imagem preferida na **taxa de Captura**.
        1. Selecione o número total de imagens que pretende recolher no **Target**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Ecrã de captura de imagem.":::

Todas as imagens estarão acessíveis em [Visão Personalizada.](https://www.customvision.ai/)

## <a name="next-steps"></a>Passos seguintes

[Teste e retreine o seu modelo de visão sem código](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).