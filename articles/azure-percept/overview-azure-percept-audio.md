---
title: Visão geral do dispositivo Azure Percept Audio
description: Saiba mais sobre a Azure Percept Audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564267"
---
# <a name="introduction-to-azure-percept-audio"></a>Introdução ao Azure Percept Audio

Azure Percept Audio é um dispositivo acessório que adiciona capacidades de IA de fala a [Azure Percept DK](./overview-azure-percept-dk.md). Contém um processador de áudio pré-configurado e uma matriz linear de quatro microfones, que lhe permite utilizar comandos de voz, observação de palavras-chave e discurso de campo distante com a ajuda dos Serviços Cognitivos Azure. É integrado fora da caixa com Azure Percept DK, [Azure Percept Studio,](https://go.microsoft.com/fwlink/?linkid=2135819)e outros serviços de gestão de borda azure. A Azure Percept Audio está disponível para compra na loja online da [Microsoft.](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

> [!div class="nextstepaction"]
> [Comprar agora](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Componentes Azure Percept Audio

O Azure Percept Audio contém os seguintes componentes principais:

- Dispositivo de áudio Azure Percept (SoM) pronto para a produção com uma matriz linear de quatro microfones e processamento de áudio via XMOS Codec
- Placa de desenvolvedor (interposer): botões 2x, 3x LEDs, Micro USB e tomada de áudio de 3,5 mm
- Cabos necessários: cabo FPC, USB Micro Type-B a USB-A
- Cartão de boas-vindas
- Placa de montagem mecânica com montagem integrada da série 80/20 1010

## <a name="compute-capabilities"></a>Capacidades de computação 

O Azure Percept Audio transmite a entrada de áudio através da pilha de discursos que funciona no CPU da placa de porta-aviões Azure Percept DK de forma híbrida. Por isso, o Azure Percept Audio requer uma placa de transporte com um SISTEMA que suporte a pilha de discursos para executar. 

O processamento de áudio é feito da seguinte forma: 

- Azure Percept Audio: captura e converte o áudio e envia-o para o DK e para o áudio jack.

- Azure Percept DK: a pilha de discursos executa o cancelamento do feixe formando e eco e processa o áudio de entrada para otimizar para a fala. Após o processamento, executa a deteção de palavras-chave.

- Nuvem: processa comandos e frases de linguagem natural, verificação de palavras-chave e reciclagem. 

- Offline: se o dispositivo estiver offline, detetará a palavra-chave e capturará a telemetria do estado da ligação à Internet. Uma taxa de aceitação falsa aumentada para a deteção de palavras-chave pode ser observada, uma vez que a verificação de palavras-chave na nuvem não pode ser realizada. 

## <a name="getting-started"></a>Introdução

- [Monte o seu Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Ligue o seu dispositivo Azure Percept Audio ao seu devkit](./quickstart-percept-audio-setup.md)
- [Complete a experiência de configuração Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Construir um protótipo sem código

Construa uma [solução de discurso sem código](./tutorial-no-code-speech.md) no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) usando modelos de assistente de voz Azure Percept para cenários de hospitalidade, saúde, inventário e automóvel.

### <a name="manage-your-no-code-speech-solution"></a>Gerencie a sua solução de fala sem código

- [Configure o seu assistente de voz no Azure Percept Studio](./how-to-manage-voice-assistant.md)
- [Configure o seu assistente de voz no Iot Hub](./how-to-configure-voice-assistant.md)
- [Azure Percept Resolução de problemas](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Informação técnica adicional

- [Folha de dados Azure Percept Audio](./azure-percept-audio-datasheet.md)
- [Comportamento de botão e LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Compre um dispositivo Azure Percept Audio na loja online da Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
