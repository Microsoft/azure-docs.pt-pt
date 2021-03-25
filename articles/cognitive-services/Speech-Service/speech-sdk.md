---
title: Sobre o Discurso SDK - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O kit de desenvolvimento de software speech (SDK) expõe muitas das capacidades do serviço Speech, facilitando o desenvolvimento de aplicações ativadas pela fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: afba973570d75eace8cae8d1ed6ed470db21ef0e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043262"
---
# <a name="about-the-speech-sdk"></a>Acerca do SDK de Voz

O kit de desenvolvimento de software speech (SDK) expõe muitas das capacidades do serviço Speech, para capacitar-te a desenvolver aplicações ativadas pela fala. O Speech SDK está disponível em muitas linguagens de programação e em todas as plataformas.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Capacidades de cenário

O Speech SDK expõe muitas características do serviço de Discurso, mas nem todas. As capacidades do SDK do Discurso estão frequentemente associadas a cenários. O Speech SDK é ideal para cenários em tempo real e não em tempo real, utilizando dispositivos locais, ficheiros, armazenamento de blob Azure e até fluxos de entrada e saída. Quando um cenário não for alcançável com o Discurso SDK, procure uma alternativa de API REST.

### <a name="speech-to-text"></a>Conversão de voz em texto

[A palavra-a-texto](speech-to-text.md) (também conhecida como *reconhecimento de voz)* transcreve fluxos de áudio para texto que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Use o discurso-a-texto com [a Compreensão da Linguagem (LUIS)](../luis/index.yml) para obter as intenções dos utilizadores da fala transcrita e agir em comandos de voz. Utilize a [Tradução de Discurso](speech-translation.md) para traduzir a entrada da fala para uma língua diferente com uma única chamada. Para obter mais informações, consulte [os fundamentos de voz a texto.](./get-started-speech-to-text.md)

**Os recipientes de Reconhecimento de Voz (SR), Lista de Frases, Intenções, Tradução e Contentores no local** estão disponíveis nas seguintes plataformas:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unidade & Xamarin & Linux & macOS
  - Java (Jre e Android)
  - JavaScript (Brower e NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (apenas SR)

### <a name="text-to-speech"></a>Conversão de texto em voz

[O texto-a-discurso](text-to-speech.md) (também conhecido como *síntese da fala*) converte o texto em discurso sintetizado semelhante ao humano. O texto de entrada é literal de cordas ou utiliza a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Para obter mais informações sobre vozes padrão ou neurais, consulte [linguagem text-to-voice e suporte de voz](language-support.md#text-to-speech).

**O texto-a-discurso (TTS)** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C#/Windows & UWP & Unidade
  - Java (Jre e Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST A API pode ser utilizada em todas as outras situações.

### <a name="voice-assistants"></a>Assistentes de voz

[Os assistentes de](voice-assistants.md) voz que utilizam o SDK do Discurso permitem-lhe criar interfaces de conversação naturais e humanas para as suas aplicações e experiências. O Speech SDK proporciona uma interação rápida e fiável que inclui dados de fala a texto, texto-a-fala e dados de conversação numa única ligação. A sua implementação pode utilizar o canal de Fala de Linha Direta do Quadro Bot ou o serviço integrado de Comandos Personalizados para a conclusão da tarefa. Além disso, os assistentes de voz podem usar vozes personalizadas criadas no Portal de [Voz Personalizada](https://aka.ms/customvoice) para adicionar uma experiência única de saída de voz.

**O** suporte do assistente de voz está disponível nas seguintes plataformas:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Dispositivos de Fala SDK)
  - Go

#### <a name="keyword-spotting"></a>Observação de palavras-chave

O conceito de observação de [palavras-chave](./custom-keyword-basics.md) é suportado no SDK do discurso. A deteção de palavras-chave é o ato de identificar uma palavra-chave na fala, seguida de uma ação ao ouvir a palavra-chave. Por exemplo, "Hey Cortana" ativaria o assistente cortana.

**O Ponto de Palavra-Chave (KWS)** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Dispositivos de Fala SDK)
  - A funcionalidade de deteção de palavras-chave (KWS) pode funcionar com qualquer tipo de microfone, o suporte oficial da KWS, no entanto, está atualmente limitado às matrizes de microfone encontradas no hardware DK do Azure Kinect ou no SDK dos Dispositivos de Fala

### <a name="meeting-scenarios"></a>Cenários de reunião

O Speech SDK é perfeito para transcrever cenários de reunião, seja a partir de um único dispositivo ou de uma conversa multi-dispositivo.

#### <a name="conversation-transcription"></a>Transcrição de Conversas

[A transcrição de conversação](conversation-transcription.md) permite o reconhecimento de voz em tempo real (e assíncronia) na identificação dos altifalantes e na atribuição de frases a cada orador (também conhecido como *diarização).* É perfeito para transcrever reuniões presenciais com a capacidade de distinguir os falantes.

**A Transcrição de Conversa** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Dispositivos de Fala SDK)

#### <a name="multi-device-conversation"></a>Conversa multi-dispositivo

Com [o Multi-device Conversation](multi-device-conversation.md), ligue vários dispositivos ou clientes numa conversação para enviar mensagens baseadas em discursos ou texto, com suporte fácil para transcrição e tradução.

**O Multi-dispositivo Conversation** está disponível nas seguintes plataformas:

  - C++/Windows
  - C# (Quadro & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Cenários personalizados/de agente

O SDK de discurso pode ser usado para transcrever cenários de call center, onde os dados da telefonia são gerados.

#### <a name="call-center-transcription"></a>Transcrição de Centros de Atendimento Telefónico

[A transcrição do Call Center](call-center-transcription.md) é um cenário comum para a transcrição de voz a texto para transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como a Interactive Voice Response (IVR). Os últimos modelos de reconhecimento de voz do serviço Desíofone primam pela transcrição destes dados da telefonia, mesmo nos casos em que os dados são difíceis de entender por um ser humano.

**A Transcrição do Call Center** está disponível através do Serviço de Fala de Lote através da sua API REST e pode ser usada em qualquer situação.

### <a name="codec-compressed-audio-input"></a>Entrada de áudio comprimido codec

Várias das linguagens de programação do SDK do Discurso suportam fluxos de entrada de áudio comprimido codec. Para obter mais informações, consulte <a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">os formatos de entrada de áudio comprimido . </a>

**A entrada de áudio comprimido codec** está disponível nas seguintes plataformas:

  - C++/Linux
  - C#/Linux
  - Java/Linux, Android e iOS

## <a name="rest-api"></a>API REST

Embora o SDK de discurso cubra muitas capacidades de recurso do Serviço de Fala, para alguns cenários poderá querer utilizar a API REST.

### <a name="batch-transcription"></a>Transcrição em lote

[A transcrição do lote](batch-transcription.md) permite a transcrição assíncronus de discurso a texto de grandes volumes de dados. A transcrição do lote só é possível a partir da API REST. Além de converter áudio de fala em texto, o lote de discurso em texto também permite a diarização e análise de sentimentos.

## <a name="customization"></a>Personalização

O Serviço de Fala oferece uma grande funcionalidade com os seus modelos padrão em termos de discurso a texto, texto-a-discurso e tradução de voz. Por vezes, pode querer aumentar o desempenho da linha de base para trabalhar ainda melhor com o seu caso de utilização único. O Serviço de Fala tem uma variedade de ferramentas de personalização sem código que facilitam e permitem criar uma vantagem competitiva com modelos personalizados com base nos seus próprios dados. Estes modelos só estarão disponíveis para si e para a sua organização.

### <a name="custom-speech-to-text"></a>Discurso-a-texto personalizado

Ao utilizar o discurso-texto para reconhecimento e transcrição num ambiente único, pode criar e formar modelos acústicos, linguísticos e de pronúncia personalizados para abordar o ruído ambiente ou o vocabulário específico da indústria. A criação e gestão de modelos de discurso personalizado sem código está disponível através do Portal da [Fala Personalizada.](https://aka.ms/customspeech) Uma vez publicado o modelo De Discurso Personalizado, pode ser consumido pelo Speech SDK.

### <a name="custom-text-to-speech"></a>Texto-a-discurso personalizado

O texto-a-discurso personalizado, também conhecido como Custom Voice é um conjunto de ferramentas online que lhe permitem criar uma voz única e reconhecível para a sua marca. A criação e gestão de modelos de Voz Personalizada sem código está disponível através do [Portal de Voz Personalizada.](https://aka.ms/customvoice) Uma vez publicado o modelo De Voz Personalizada, pode ser consumido pelo Speech SDK.

## <a name="get-the-speech-sdk"></a>Obtenha o Discurso SDK

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

* [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer a fala em C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)