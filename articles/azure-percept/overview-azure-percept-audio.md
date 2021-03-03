---
title: Visão geral do dispositivo Azure Percept Audio
description: Saiba mais sobre a Azure Percept Audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663686"
---
# <a name="introduction-to-azure-percept-audio"></a>Introdução ao Azure Percept Audio

Azure Percept Audio é um dispositivo acessório que adiciona capacidades de IA de fala ao Azure Percept DK. Contém um acelerador de IA de fala pré-configurado e uma matriz linear de quatro microfones, que lhe permite aplicar comandos personalizados, observação de palavras-chave e discurso de campo distante para dispositivos de audição locais. O Azure Percept Audio permite que os fabricantes de dispositivos alarguem as capacidades de visão Azure Percept DK a novos dispositivos inteligentes ativados por voz. É integrado fora da caixa com Azure Percept DK, Azure Percept Studio, e outros serviços de gestão de borda azure. Está disponível para compra na loja online da [Microsoft.](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

![Dispositivo Azure Percept Audio.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Componentes Azure Percept Audio

O Azure Percept Audio contém os seguintes componentes principais:

- Dispositivo de áudio Azure Percept (SoM) pronto para a produção com matriz linear de quatro microfones
- Placa de programação (inclui botões 2x, 3x LEDs, USB Micro e 3,5 mm Audio Jack)
- Cabos necessários: Cabo flex, USB Micro Type-B a USB-A
- Cartão de boas-vindas
- Placa de montagem mecânica com montagem integrada da série 80/20 1010


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Introdução

- [Monte o seu Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Complete a experiência de configuração Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Ligue o seu dispositivo Azure Percept Audio ao seu devkit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Construir um protótipo sem código

Construa uma [solução de discurso sem código](./tutorial-no-code-speech.md) usando modelos de assistente de voz Azure Percept para cenários de hospitalidade, saúde, inventário e automóvel.

## <a name="additional-technical-information"></a>Informação técnica adicional

- [Folha de dados Azure Percept Audio](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Passos seguintes

Encomende um dispositivo Azure Percept Audio na [loja online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)da Microsoft .