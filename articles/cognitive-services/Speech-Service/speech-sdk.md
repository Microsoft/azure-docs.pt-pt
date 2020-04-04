---
title: Sobre o Discurso SDK - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: O kit de desenvolvimento de software Speech (SDK) expõe muitas das capacidades do serviço speech, facilitando o desenvolvimento de aplicações ativadas pela fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656569"
---
# <a name="about-the-speech-sdk"></a>Acerca do SDK de Voz

O kit de desenvolvimento de software Speech (SDK) expõe muitas das capacidades do serviço speech, para capacitá-lo para desenvolver aplicações ativadas pela fala. O Speech SDK está disponível em muitas linguagens de programação e em todas as plataformas.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Capacidades de cenário

O SDK do Discurso expõe muitas características do serviço de Discurso, mas não todas. As capacidades do SDK do Discurso estão frequentemente associadas a cenários. O Speech SDK é ideal tanto para cenários em tempo real como para não em tempo real, utilizando dispositivos locais, ficheiros, armazenamento de blob Azure e até mesmo fluxos de entrada e saída. Quando um cenário não for alcançável com o SDK do Discurso, procure uma alternativa rest API.

### <a name="speech-to-text"></a>Conversão de voz em texto

[O discurso ao texto](speech-to-text.md) (também conhecido como reconhecimento da *fala)* transcreve streams de áudio para texto que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Use o discurso a texto com a compreensão da [linguagem (LUIS)](../luis/index.yml) para derivar as intenções dos utilizadores de falar transcrito e agir sobre comandos de voz. Use [a Tradução da Fala](speech-translation.md) para traduzir a entrada da fala para uma linguagem diferente com uma única chamada. Para mais informações, consulte os [fundamentos do Discurso ao Texto](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Conversão de texto em voz

[Texto-a-fala](text-to-speech.md) (também conhecido como síntese da *fala)* converte texto em discurso sintetizado como o humano. O texto de entrada é literal de cordas ou utiliza a linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md). Para obter mais informações sobre vozes padrão ou neurais, consulte a [linguagem texto-a-voz e](language-support.md#text-to-speech)o suporte de voz .

### <a name="voice-assistants"></a>Assistentes de voz

Os assistentes de voz que usam o SDK do Discurso permitem que os desenvolvedores criem interfaces de conversação naturais e humanas para as suas aplicações e experiências. O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e um assistente. A implementação utiliza o canal de discurso de linha direta do Bot Framework ou o serviço integrado de Comandos Personalizados (Pré-visualização) para a conclusão da tarefa. Além disso, os assistentes de voz podem ser criados usando o [Custom Voice Portal](https://aka.ms/customvoice) para criar uma experiência de voz única.

#### <a name="keyword-spotting"></a>Spotting de palavra-chave

O conceito de spotting de [palavras-chave](speech-devices-sdk-create-kws.md) é apoiado no SDK do discurso. A identificação das palavras-chave é o ato de identificar uma palavra-chave no discurso, seguida de uma ação ao ouvir a palavra-chave. Por exemplo, "Hey Cortana" ativaria o assistente cortana.

### <a name="meeting-scenarios"></a>Cenários de reunião

O Speech SDK é perfeito para transcrever cenários de reunião, seja a partir de um único dispositivo ou de uma conversa multi-dispositivo.

#### <a name="conversation-transcription"></a>Transcrição de Conversas

[A Transcrição da Conversa](conversation-transcription.md) permite o reconhecimento da fala em tempo real (e assíncrono), a identificação do altifalante e a atribuição de frases a cada orador (também conhecido como *diarização).* É perfeito para transcrever reuniões presenciais com a capacidade de distinguir os falantes.

#### <a name="multi-device-conversation"></a>Conversa ção multi-dispositivo

Com a [Conversa ção multi-dispositivo,](multi-device-conversation.md)ligue vários dispositivos ou clientes numa conversa para enviar mensagens baseadas em discursos ou com texto, com suporte fácil para transcrição e tradução.

### <a name="custom--agent-scenarios"></a>Cenários personalizados/agentes

O SDK do Discurso pode ser usado para transcrever cenários de call center, onde os dados da telefonia são gerados.

#### <a name="call-center-transcription"></a>Transcrição de Centros de Atendimento Telefónico

[A transcrição](call-center-transcription.md) do Call Center é um cenário comum para o discurso-texto para transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como a Interactive Voice Response (IVR). Os mais recentes modelos de reconhecimento da fala do serviço da Fala primam pela transcrição destes dados de telefonia, mesmo nos casos em que os dados são difíceis de entender por um ser humano.

### <a name="codec-compressed-audio-input"></a>Entrada de áudio comprimido codec

Várias das linguagens de programação do Speech SDK suportam fluxos de entrada de áudio comprimidos codec. Para mais informações, consulte <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">a utilização de formatos <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>de entrada de áudio comprimidos .

## <a name="rest-api"></a>API REST

Enquanto o SDK de Discurso cobre muitas capacidades de recurso do Serviço de Fala, para alguns cenários você pode querer usar a API REST. Como exemplo, a gestão de pontos finais só é exposta através da API REST.

> [!TIP]
> Ao confiar na API REST, use o Editor Swagger para gerar automaticamente bibliotecas de clientes. Por exemplo, para gerar uma biblioteca de clientes de transcrição de Lote.
> 1. Copie o URL do exemplo abaixo:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. Navegue para o <a href="https://editor.swagger.io/" target="_blank">Editor <span class="docon docon-navigate-external x-hidden-focus"></span> Swagger</a>
> 1. Selecione URL de**importação** de **ficheiros** > e colá o URL
> 1. Selecione **Generate Client** e escolha a sua linguagem de programação desejada

### <a name="batch-transcription"></a>Transcrição em lote

[A transcrição](batch-transcription.md) do lote permite a transcrição assíncrona da fala-a-texto de grandes volumes de dados. A transcrição do lote só é possível a partir da API REST.

## <a name="customization"></a>Personalização

O Speech Service oferece uma grande funcionalidade com os seus modelos padrão em todo o discurso a texto, texto-a-fala e tradução da fala. Às vezes, pode querer aumentar o desempenho de base para trabalhar ainda melhor com o seu caso de uso único. O Speech Service tem uma variedade de ferramentas de personalização sem código que facilitam a sua vida e permitem criar uma vantagem competitiva com modelos personalizados com base nos seus próprios dados. Estes modelos só estarão disponíveis para si e para a sua organização.

### <a name="custom-speech-to-text"></a>Discurso personalizado a texto

Ao utilizar o discurso ao texto para reconhecimento e transcrição num ambiente único, pode criar e formar modelos acústicos, linguísticos e de pronúncia personalizados para abordar o ruído ambiente ou o vocabulário específico da indústria. A criação e gestão de modelos de Discurso Personalizado sem código está disponível através do Portal da [Fala Personalizada.](https://aka.ms/customspeech) Uma vez publicado o modelo de Discurso Personalizado, pode ser consumido pelo SDK da Fala.

### <a name="custom-text-to-speech"></a>Texto-a-fala personalizado

Texto-a-voz personalizado, também conhecido como Custom Voice é um conjunto de ferramentas online que lhe permitem criar uma voz única e reconhecível para a sua marca. A criação e gestão de modelos custom Voice sem código está disponível através do Portal de [Voz Personalizada.](https://aka.ms/customvoice) Uma vez publicado o modelo Custom Voice, pode ser consumido pelo SDK da Fala.

## <a name="get-the-speech-sdk"></a>Obtenha o SDK do Discurso

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Browser](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer o discurso em C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
