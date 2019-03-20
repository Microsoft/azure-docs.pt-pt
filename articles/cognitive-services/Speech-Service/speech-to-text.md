---
title: Conversão de voz em texto com os serviços de voz do Azure
titleSuffix: Azure Cognitive Services
description: Conversão de voz em texto dos serviços de voz do Azure, também conhecido como conversão de voz em texto, permite transcrição em tempo real dos fluxos de áudio em texto que podem consumir os seus aplicativos, ferramentas ou dispositivos, apresentar e tomar medidas em como entrada de comando. Este serviço utiliza a tecnologia da mesma tecnologia de reconhecimento que a Microsoft utiliza para produtos da Cortana e o Office e funciona de forma totalmente integrada com a tradução e voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: acf338afdf33408637bf7cca79d28f5e90888eba
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224445"
---
# <a name="what-is-speech-to-text"></a>O que é a conversão de voz em texto?

Conversão de voz em texto dos serviços de voz do Azure, também conhecido como conversão de voz em texto, permite transcrição em tempo real dos fluxos de áudio em texto que podem consumir os seus aplicativos, ferramentas ou dispositivos, apresentar e tomar medidas em como entrada de comando. Este serviço utiliza a tecnologia da mesma tecnologia de reconhecimento que a Microsoft utiliza para produtos da Cortana e o Office e funciona de forma totalmente integrada com a tradução e voz.  Para obter uma lista completa de idiomas de voz em texto disponíveis, consulte [idiomas suportados](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#speech-to-text).

Por predefinição, o serviço de voz em texto utiliza o modelo de idioma Universal. Este modelo foi treinado a utilizar os dados pertencentes à Microsoft e é implementado na cloud. É ideal para conversação e cenários de ditado. Se estiver usando a voz em texto para o reconhecimento e transcrição de num ambiente exclusivo, pode criar e formar modelos acústicos, idioma e pronúncia personalizados para o ruído do endereço ou específicos da indústria vocabulário. 

Pode facilmente capturar áudio de um microfone, ler a partir de um fluxo ou acessar arquivos de áudio do armazenamento com o SDK de voz e REST APIs. O SDK de voz suporta WAV/PCM 16 bits, 16 kHz, canal único áudio para reconhecimento de fala. Formatos de áudio adicionais são suportados com o [ponto final REST de voz em texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-formats) ou o [transcrição serviço batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Principais recursos

Seguem-se as funcionalidades disponíveis através do SDK de voz e REST APIs:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Transcrição de expressões com curtos (< 15 segundos). Só suporta o resultado final transcrição. | Sim | Sim |
| Transcrição contínua de expressões com longo e transmissão em fluxo de áudio (> 15 segundos). Suporta os resultados de transcrição intermediárias e finais. | Sim | Não |
| Derivar intenções de resultados de reconhecimento com [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Sim | Não\* |
| Batch transcrição de arquivos de áudio de forma assíncrona. | Não | Sim\** |
| Criar e gerir modelos de voz. | Não | Sim\** |
| Criar e gerir implementações de modelo personalizado. | Não | Sim\** |
| Crie testes de precisão para medir a precisão do modelo de linha de base em comparação com os modelos personalizados. | Não | Sim\** |
| Gerir subscrições. | Não | Sim\** |

\* *LUIS intenções e entidades podem ser obtidas através de uma subscrição separada do LUIS. Com esta subscrição, o SDK pode chamar o LUIS para e fornecer a entidade e resultados de intenção. Com a API REST, pode chamar o LUIS para derivar intenções e entidades com a sua subscrição do LUIS.*

\** *Esses serviços estão disponíveis com o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Introdução à conversão de voz em texto

Oferecemos inícios rápidos em linguagens de programação mais populares, cada um concebido para que executar o código em menos de 10 minutos. Esta tabela inclui uma lista completa dos inícios rápidos do SDK de voz organizados por idioma.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Procurar](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Procurar](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Procurar](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Procurar](https://aka.ms/csspeech/javaref) |
| [JavaScript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Procurar](https://aka.ms/AA434tv) |
| [Javascript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Procurar](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Procurar](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Procurar](https://aka.ms/AA434tr)  |

Se preferir utilizar o serviço REST de voz em texto, veja [REST APIs](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de exemplo

Depois de teve a oportunidade de se usar os serviços de voz, experimente o nosso tutorial que ensina a reconhecer intenções de voz com o SDK de voz e o LUIS.

* [Tutorial: Reconhecer intenções de conversão de voz com o SDK de voz e o LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Código de exemplo para o SDK de voz está disponível no GitHub. Esses exemplos abrangem os cenários comuns, como ler o áudio de um arquivo ou fluxo, reconhecimento contínuo e única e trabalhar com modelos personalizados.

* [Exemplos de voz em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição do batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo Universal utilizado pelos serviços de voz, é possível criar modelos acústicos, idioma e pronúncia personalizados específicos à sua experiência. Aqui está uma lista de opções de personalização:

| Modelo | Descrição |
|-------|-------------|
| [Modelo acústico](how-to-customize-acoustic-models.md) | A criação de um modelo acústico personalizado é útil se seu aplicativo, ferramentas ou dispositivos são usados num ambiente específico, como num carro ou fábrica com condições de gravação específico. Os exemplos envolvem voz acentuada, ruídos de fundo específicos ou utilizar um microfone específico para gravação. |
| [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de idioma personalizado para melhorar a transcrição de vocabulário específicos da indústria e de gramática, como a terminologia médica ou gíria, IT. |
| [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizado, pode definir o formulário fonético e a exibição de uma palavra ou o termo. É útil para lidar com os termos personalizados, tais como nomes de produto ou acrônimos. Tudo o que precisa para começar a utilizar é um ficheiro de pronúncia – um arquivo. txt simples. |

> [!NOTE]
> Opções de personalização variam consoante o idioma/região (consulte [idiomas suportados](supported-languages.md)).

## <a name="migration-guides"></a>Guias de migração

> [!WARNING]
> Voz do Bing será encerrado a 15 de Outubro de 2019.

Se seus aplicativos, ferramentas ou produtos estiver a utilizar as APIs de voz do Bing ou voz personalizada, nós criamos guias para o ajudar a migrar para os serviços de voz.

* [Migrar de voz do Bing para os serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrar de voz personalizada para os serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
* [Obtenha o SDK de voz](speech-sdk.md)
