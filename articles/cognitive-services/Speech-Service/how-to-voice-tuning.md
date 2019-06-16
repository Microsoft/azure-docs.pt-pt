---
title: Ajustar a saída de texto para discurso - serviços de voz
titleSuffix: Azure Cognitive Services
description: Ative o registo no SDK de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 8d3e25f8217f3cc8772de9fbbb06a407008ca6f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593886"
---
# <a name="fine-tune-text-to-speech-output"></a>Ajustar a saída de conversão de texto em voz

Serviços de voz do Azure permitem-lhe ajustar a velocidade, pronúncia, volume, pitch e contour de voz de saída usando [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML é uma linguagem de marcação baseada em XML que usa marcas para informar o serviço sobre que funcionalidades preciso ajustar. Em seguida, é enviada a mensagem SSML no corpo de cada pedido para o serviço de texto para voz. Para simplificar o processo de personalização, os serviços de voz oferecem agora uma [otimização de voz](https://aka.ms/voicetuning) saídas de ferramenta que permite-lhe inspecionar visualmente e ajustar o texto para voz em tempo real.

A ferramenta de otimização de voz oferece suporte da Microsoft [padrão](language-support.md#standard-voices), [neural](language-support.md#text-to-speech), e [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Comece com a ferramenta de otimização de voz

Antes de começar a ajustar a saída de texto para voz com a ferramenta de otimização de voz, terá de concluir estes passos:

1. Criar uma [Microsoft conta gratuita](https://account.microsoft.com/account) se ainda não tiver uma.
2. Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma. Clique em **começar gratuitamente**e criar uma nova conta do Azure com a sua conta Microsoft.

3. Crie uma subscrição de serviços de voz no portal do Azure. Instruções passo a passo para [como criar um recurso de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) estão disponíveis.
   >[!NOTE]
   >Quando cria um recurso de voz no portal do Azure, as informações de localização do Azure tem de corresponder com a região de voz TTS. Voz TTS neural suporta um conjunto de sub-rotina de localizações do Azure. Para obter uma lista completa de suporte, consulte [regiões](regions.md#text-to-speech).

   >[!NOTE]
   >Tem de ter um F0 ou uma chave de S0 criada no portal do Azure antes de poder utilizar o serviço. Ajuste de voz **não** suporta o [chave de avaliação gratuita de 30 dias](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Inicie sessão para o [otimização de voz](https://aka.ms/voicetuning) portal e ligue-se a sua subscrição de serviços de voz. Escolha uma única subscrição de serviços de voz e, em seguida, crie um projeto.
5. Selecione **novo ajuste**. Em seguida, siga estes passos:

   * Localize e selecione **todas as subscrições**.  
   * Selecione **ligar a subscrição existente**.  
     ![Ligar uma subscrição existente](./media/custom-voice/custom-voice-connect-subscription.png).
   * Introduza a chave de subscrição de serviços de voz do Azure, em seguida, selecione **adicionar**. As chaves de subscrição estão disponíveis no portal de personalização de voz na [página de subscrição](https://go.microsoft.com/fwlink/?linkid=2090458). Também foi possível obter as chaves a partir do painel de gestão de recursos no [portal do Azure](https://portal.azure.com/).
   * Se tiver mais de uma subscrição de serviços de voz que planeia utilizar o, repita estes passos para cada subscrição.

## <a name="customize-the-text-to-speech-output"></a>Personalizar a saída de texto para discurso

Agora que criar contas e vincular a sua subscrição, pode iniciar a otimização a saída de texto para voz.

1. Escolha uma voz.
2. Introduza o texto que pretende editar.
3. Antes de começar a fazer edições, reproduza áudio para ter uma noção da saída.
4. Selecione a word/frase que queira refinar e comece a experimentar com as funções com base em SSML diferentes.

>[!TIP]
> Para obter informações detalhadas sobre como ajustar SSML e otimizar a saída de voz, consulte [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limitações

Otimização de voz neural é ligeiramente diferente de ajuste para vozes padrão e personalizados.

* Intonation não é suportada para vozes Neural.
* Recursos de argumento de venda e de volume só funcionam com sentenças completas. Estas funcionalidades não estão disponíveis ao nível do word.
* Para obter uma taxa, alguns vozes Neural podem ser ajustados baseados em palavras, enquanto outras precisam de selecionar todos frases.

> [!TIP]
> A ferramenta de otimização de voz fornece informações contextuais sobre recursos e a otimização.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um recurso de fala no Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Iniciar a otimização de voz](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
