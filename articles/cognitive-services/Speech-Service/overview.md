---
title: Quais são os serviços de voz do Azure?
titleSuffix: Azure Cognitive Services
description: Os serviços de voz do Azure são unificação de voz em texto, voz e tradução de voz numa única subscrição do Azure. É fácil adicionar voz seus aplicativos, ferramentas e dispositivos com o SDK de voz, o SDK de dispositivos de voz ou REST APIs. Adicionar a funcionalidade de voz a um chatbot existente, converter voz num aplicativo de tradução ou a transcrição de grandes volumes de dados do Centro de chamada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 4a9af96c1defab4cbb10d917b5faa7e0e3ab837f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485069"
---
# <a name="what-are-the-speech-services"></a>O que são os Serviços de Voz?

Serviços de voz do Azure estão a unificação de voz em texto, voz e tradução de voz numa única subscrição do Azure. É fácil em voz para ativar as suas aplicações, ferramentas e dispositivos com o [SDK de voz](speech-sdk-reference.md), [SDK de dispositivos de voz](https://aka.ms/sdsdk-quickstart), ou [REST APIs](rest-apis.md).

> [!IMPORTANT]
> Serviços de voz foi substituída por uma API de voz do Bing, de voz do Translator e conversão de voz personalizada. Ver *guias de procedimentos > migração* para obter instruções de migração.

Esses recursos constituem os serviços de voz do Azure. Utilize as ligações nesta tabela para obter mais informações sobre os casos de utilização comuns para cada funcionalidade ou navegue até a referência de API.

| Serviço | Funcionalidade | Descrição | SDK | REST |
|---------|---------|-------------|-----|------|
| [Conversão de voz em texto](speech-to-text.md) | Conversão de voz em texto | Conversão de voz em texto converte os fluxos de áudio em texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou apresentar. Utilize a voz para texto com [compreensão de idiomas (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) derivar intenções de utilizador de fala transcrita e tomar medidas relacionadas com comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transcrição de batch](batch-transcription.md) | Transcrição de batch permite assíncrona transcrição de voz em texto de grandes volumes de dados. Este é um serviço baseado em REST, que utiliza o mesmo ponto final como a personalização e a gestão de modelos. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Transcrição da conversação](conversation-transcription-service.md) | Permite que o reconhecimento de voz em tempo real, identificação de orador e diarization. Ele é perfeito para fotografar reuniões presenciais com a capacidade de distinguir os oradores. | Sim | Não |
| | [Criar modelos de voz personalizada](#customize-your-speech-experience) | Se estiver usando a voz em texto para o reconhecimento e transcrição de num ambiente exclusivo, pode criar e formar modelos acústicos, idioma e pronúncia personalizados para o ruído do endereço ou específicos da indústria vocabulário. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Voz](text-to-speech.md) | Conversão de texto em voz | Voz converte a introdução de texto em fala sintetizada de semelhante à humana usando [Speech Synthesis Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Escolha entre vozes padrão e vozes neurais (consulte [suporte de idiomas](language-support.md)). | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie tipos de voz personalizada exclusivo para a sua marca ou o produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Voz](speech-translation.md) | Tradução de voz | Tradução de voz permite a tradução em tempo real, vários idioma de voz às suas aplicações, ferramentas e dispositivos. Utilize este serviço para a tradução de voz para voz e conversão de voz em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz em primeiro lugar Virtual](voice-first-virtual-assistants.md) | Assistentes de voz em primeiro lugar virtual | Assistentes de virtual personalizados, usando os serviços de voz do Azure permitem aos programadores criar interfaces conversacionais naturais, semelhante à humana para seus aplicativos e experiências. Canal de voz de linha direta do Bot Framework aprimora esses recursos, fornecendo um ponto de entrada organizada, coordenada para um bot compatível, que permite a voz na voz horizontalmente interação com baixa latência e alta fiabilidade. | [Sim](voice-first-virtual-assistants.md) | Não |

## <a name="news-and-updates"></a>Notícias e atualizações

Saiba quais são as novidades dos serviços de voz do Azure.

* Junho de 2019
    * Lançado voz SDK 1.6.0. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
* Maio de 2019 - documentação agora está disponível para [transcrição da conversação](conversation-transcription-service.md), [transcrição do Centro de chamar](call-center-transcription.md), e [assistentes de voz em primeiro lugar Virtual](voice-first-virtual-assistants.md).
* Maio de 2019
    * Lançado voz SDK 1.5.1. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
    * Lançado 1.5.0 do SDK de voz. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
* Abril de 2019 - lançados voz SDK 1.4.0 com suporte para voz (Beta) for C++, C#e o Java em Windows e Linux. Além disso, o SDK suporta agora formatos de áudio MP3 e Opus/Ogg para C++ e C# no Linux. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
* De 2019 de Março - já está disponível um novo ponto final de voz (TTS), que retorna uma lista completa de vozes disponíveis numa região específica. Além disso, as novas regiões são agora suportados para TTS. Para obter mais informações, consulte [referência da API de voz (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Experimente os serviços de voz

Oferecemos inícios rápidos em linguagens de programação mais populares, cada um concebido para que executar o código em menos de 10 minutos. Esta tabela contém os inícios rápidos mais populares para cada funcionalidade. Utilize o painel de navegação esquerda para explorar outros idiomas e plataformas.

| Conversão de voz em texto (SDK) | Voz (SDK) | Tradução (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (navegador)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Conversão de voz em texto e texto para discurso também têm pontos finais REST e guias de introdução associados.

Depois de teve a oportunidade de se usar os serviços de voz, experimente o nosso tutorial que ensina a reconhecer intenções de voz com o SDK de voz e o LUIS.

* [Tutorial: Reconhecer intenções de conversão de voz com o SDK de voz e o LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Tutorial: Criar uma aplicação Flask traduzir texto, analisar sentimentos e sintetizar traduzido texto em voz, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

Código de exemplo está disponível no GitHub para cada um dos serviços de voz do Azure. Esses exemplos abrangem os cenários comuns, como ler o áudio de um arquivo ou fluxo, reconhecimento contínuo e única e trabalhar com modelos personalizados. Utilize estas ligações para ver exemplos do SDK e REST:

* [Exemplos de tradução de voz em texto, voz e conversão de voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição do batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de texto para voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Personalize a sua experiência de voz

Serviços de voz do Azure funciona bem com modelos incorporados, no entanto, pode querer personalizar e otimizar a experiência para o seu produto ou o ambiente ainda mais. Intervalo de opções de personalização do modelo acústico ajuste para tipos de voz exclusivo para a sua marca. Depois de criar um modelo personalizado, pode usá-lo com qualquer um dos serviços de voz do Azure.

| Serviço de Voz | Modelo | Descrição |
|----------------|-------|-------------|
| Voz em Texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Criar um modelo acústico personalizado para aplicações, ferramentas, ou dispositivos que são utilizados em particular ambientes, como num carro ou num chão de fábrica, cada um com condições de gravação específico. Os exemplos incluem voz acentuado, ruídos de fundo específico ou usando um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de idioma personalizado para melhorar a transcrição de vocabulário específico de campo e de gramática, como a terminologia médica ou gíria, IT. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizado, pode definir o formulário fonético e a exibição de uma palavra ou o termo. É útil para lidar com os termos personalizados, tais como nomes de produto ou acrônimos. Tudo o que precisa para começar a utilizar é um ficheiro de pronúncia – um arquivo. txt simples. |
| Texto em Voz | [Tipo de voz](how-to-customize-voice-font.md) | Tipos de voz personalizadas permitem-lhe criar uma voz reconhecível, um do-única para sua marca. Leva apenas uma pequena quantidade de dados para começar a utilizar. Quanto mais dados que fornecer, o mais natural e semelhante à humana soará seu tipo de voz. |

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
