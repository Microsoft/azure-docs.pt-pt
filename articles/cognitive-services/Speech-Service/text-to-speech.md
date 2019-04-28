---
title: Voz com serviços de voz do Azure
titleSuffix: Azure Cognitive Services
description: Voz, conversão de voz dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2c5c2bc2b8dd1930efef9833bd442fcad5566e2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460274"
---
# <a name="what-is-text-to-speech"></a>O que é o texto para discurso?

Voz, conversão de voz dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa, consulte [idiomas suportados](language-support.md#text-to-speech).

Tecnologia de texto para discurso permite que os criadores de conteúdo interagir com os seus utilizadores de formas diferentes. Voz pode melhorar a acessibilidade, fornecendo os utilizadores com uma opção para interagir com o conteúdo poder ouvi-lo. Se o usuário tem um visual impairment, uma deficiência de aprendizagem, ou necessita de informações de navegação, obtendo, voz pode melhorar uma experiência existente. Voz também é um complemento valioso para bots de voz e assistentes virtual.

### <a name="standard-voices"></a>Vozes padrão

Vozes padrão são criadas usando técnicas de estatísticas de síntese Paramétrica e/ou de síntese de concatenação. Estes vozes são altamente inteligível e soam bastante naturais. É possível habilitar facilmente seus aplicativos falar em mais de 45 idiomas, com uma vasta gama de opções de voz. Estes vozes fornecem precisão pronúncia elevada, incluindo suporte para abreviaturas, expansões de acrônimos, interpretações de data/hora, polyphones e muito mais. Utilize a voz como método padrão para melhorar a acessibilidade de seus aplicativos e serviços, permitindo que os utilizadores interajam com o seu conteúdo poder ouvi-lo.

### <a name="neural-voices"></a>Vozes neurais

Vozes neurais usam redes neurais profundas para superar os limites dos sistemas de texto para voz tradicionais em correspondentes aos padrões de estresse e intonation num idioma falado e resumir as unidades de conversão de voz numa voz de computador. Voz Standard divide prosody em separado de análise linguística e passos de predição acústico que são regidos pelos modelos independentes. Isso pode resultar na síntese de voz muffled, buzzy. Nossa capacidade neural faz síntese de voz e de predição de prosody simultaneamente, que resulta numa voz mais fluida e com o som natural.

Vozes neurais podem ser utilizados para tornar as interações com chatbots e assistentes virtual mais natural e envolventes converter textos digital, como o e-livros em audiobooks e melhorar a sistemas de navegação no carro. Com o prosody natural de semelhante à humana e articulation clara de palavras, o vozes Neural reduzem significativamente fadiga escuta quando interage com os sistemas de IA. Para obter mais informações sobre as vozes neurais, consulte [idiomas suportados](language-support.md#text-to-speech).

Para saber mais sobre os benefícios de vozes neurais, veja [serviço de texto para discurso neural novo da Microsoft ajuda a máquinas falar, como as pessoas](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vozes personalizadas

Personalização de voz permite-lhe criar uma voz reconhecível, um do-única para sua marca. Para criar o seu tipo de voz personalizada, fazer uma gravação de studio e carregar os scripts associados como os dados de treinamento. O serviço, em seguida, cria um modelo de voz única ajustado para a gravação. Pode utilizar este tipo de voz personalizada para sintetizar voz. Para obter mais informações, consulte [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="core-features"></a>Principais recursos

Esta tabela lista os principais recursos de voz:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Converta texto em voz. | Sim | Sim |
| Carregar conjuntos de dados para adaptação de voz. | Não | Sim\* |
| Criar e gerir modelos de tipo de letra de voz. | Não | Sim\* |
| Criar e gerir implementações de tipo de letra de voz. | Não | Sim\* |
| Criar e gerir os testes de tipo de letra de voz. | Não | Sim\* |
| Gerir subscrições. | Não | Sim\* |

\* *Esses serviços estão disponíveis com o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index). Estes formação de voz personalizada e APIs de gestão de implementam a limitação de que os pedidos de limites para 25 por 5 segundos, enquanto a síntese de fala API propriamente dita implementa limitação que permite 200 pedidos por segundo, como a mais alta. Quando ocorre a limitação, será notificado através de cabeçalhos de mensagens.*

## <a name="get-started-with-text-to-speech"></a>Introdução ao texto em voz

Oferecemos inícios rápidos, criados para executar o código em menos de 10 minutos. Esta tabela inclui uma lista de guias de introdução de texto para discurso organizados por idioma.

### <a name="sdk-quickstarts"></a>Inícios rápidos do SDK

| Início rápido (SDK) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Procurar](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Inícios rápidos REST

| Início rápido (REST) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para voz está disponível no GitHub. Esses exemplos abrangem a conversão de texto para discurso em linguagens de programação mais populares.

* [Exemplos de texto para voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de texto para voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obter uma subscrição de serviços de voz gratuito](get-started.md)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
