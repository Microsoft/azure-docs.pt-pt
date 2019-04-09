---
title: Voz com serviços de voz do Azure
titleSuffix: Azure Cognitive Services
description: Voz, conversão de voz dos serviços do Azure é um serviço baseado em REST que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 52f74bb3cb9e460fc5c572079355f47b4b0bf0a3
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010452"
---
# <a name="what-is-text-to-speech"></a>O que é o texto para discurso?

Voz, conversão de voz dos serviços do Azure é um serviço baseado em REST que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa, consulte [idiomas suportados](language-support.md#text-to-speech).

Tecnologia de texto para discurso permite que os criadores de conteúdo interagir com os seus utilizadores de formas diferentes. Voz pode melhorar a acessibilidade, fornecendo os utilizadores com uma opção para interagir com o conteúdo poder ouvi-lo. Se o usuário tem um visual impairment, uma deficiência de aprendizagem, ou necessita de informações de navegação, obtendo, voz pode melhorar uma experiência existente. Voz também é um complemento valioso para bots de voz e assistentes virtual.

### <a name="neural-voices"></a>Vozes neurais

Vozes neurais podem ser utilizados para tornar as interações com chatbots e assistentes virtual mais natural e envolventes converter textos digital, como o e-livros em audiobooks e melhorar a sistemas de navegação no carro. Com o prosody natural de semelhante à humana e articulation clara de palavras, o vozes Neural reduzem significativamente fadiga escuta quando interage com os sistemas de IA. Para obter mais informações sobre as vozes neurais, consulte [idiomas suportados](language-support.md#text-to-speech).

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

\* *Esses serviços estão disponíveis com o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index).*

> [!NOTE]
> O ponto final de texto para discurso implementa limitação que limita os pedidos para 25 por 5 segundos. Quando ocorre a limitação, será notificado através de cabeçalhos de mensagens.

## <a name="get-started-with-text-to-speech"></a>Introdução ao texto em voz

Oferecemos inícios rápidos, criados para executar o código em menos de 10 minutos. Esta tabela inclui uma lista de guias de introdução de texto para discurso organizados por idioma.

| Início rápido (REST) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

| Início rápido (SDK) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Procurar](https://aka.ms/csspeech/cppref) |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para voz está disponível no GitHub. Esses exemplos abrangem a conversão de texto para discurso em linguagens de programação mais populares.

* [Exemplos de texto para voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Exemplos de texto para voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="reference-docs"></a>Documentos de referência

* [API de Voz](speech-sdk-reference.md)
* [SDK de Dispositivos de Voz](speech-devices-sdk.md)
* [REST API: Conversão de voz em texto](rest-speech-to-text.md)
* [REST API: Conversão de texto em voz](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obter uma subscrição de serviços de voz gratuito](get-started.md)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
