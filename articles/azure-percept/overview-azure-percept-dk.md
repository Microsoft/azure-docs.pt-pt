---
title: Visão geral de Azure Percept DK
description: Saiba mais sobre o Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663470"
---
# <a name="azure-percept-dk-overview"></a>Visão geral de Azure Percept DK

Azure Percept DK é um kit de desenvolvimento de IA de borda projetado para desenvolver a prova de conceitos de IA de visão. Quando combinado com [o Azure Percept Studio,](./overview-azure-percept-studio.md)torna-se uma plataforma poderosa mas simples de usar para construir soluções de IA de ponta para uma ampla gama de aplicações de IA de visão. Está disponível para compra na loja online da [Microsoft.](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="A imagem.":::

## <a name="key-features"></a>Principais Funcionalidades

- **A capacidade de executar IA no limite.** Com a aceleração de hardware incorporada, pode executar modelos de IA de visão sem ligação à nuvem.
- **Raiz de hardware da segurança de confiança incorporada em**. Consulte esta visão geral da [Azure Percept Security](./overview-percept-security.md) para mais detalhes.
- **Integração perfeita com [o Azure Percept Studio](./overview-azure-percept-studio.md)** e outros serviços Azure. Tais como, Azure IoT Hub, Azure Cognitive Services e [Live Video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Suporte para as principais plataformas de IA.** Como ONNX e TensorFlow.
- **Integração com o sistema de gradeamento 80/20**. Facilitando a construção de protótipos em ambientes de produção. Saiba mais sobre a [integração 8/20](./overview-8020-integration.md).

## <a name="hardware-components"></a>Componentes de hardware

- O quadro de porta-aviões Azure Percept DK
    - Processador NXP iMX8m
    - Módulo de Plataforma Fidedigna (TPM) versão 2.0
    - Conectividade Wi-Fi e Bluetooth
    - Ver a folha de [dados](./azure-percept-dk-datasheet.md) completa
- O sistema Azure Percept Vision no módulo (SoM)
    - Intel Movidius Myriad X (MA2085) unidade de processamento de visão (VPU)
    - Sensor de câmara RGB com a capacidade de adicionar um segundo
    - Ver a folha de [dados](./azure-percept-vision-datasheet.md) completa

## <a name="get-started-with-the-azure-percept-dk"></a>Começar com o Azure Percept DK

- Complete estes Quick Starts
    - [Desboxe e monte o Azure Percept DK](./quickstart-percept-dk-unboxing.md)
    - [Configurar o Azure Percept DK e executar o seu primeiro modelo de IA de visão](./quickstart-percept-dk-set-up.md)
- Comece a construir a prova de conceitos com estes tutoriais
    - [Criar uma solução de visão sem código no Azure Percept Studio](./tutorial-nocode-vision.md)
    - [Criar um assistente de voz no Azure Percept Studio](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Passos seguintes

Encomende um Azure Percept DK na [loja online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)da Microsoft .