---
title: Início rápido text-to-speech - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar o SDK do discurso para converter texto-a-discurso. Neste arranque rápido, aprende-se sobre padrões de construção e design de objetos, formatos de saída de áudio suportados, o CLI do discurso e opções de configuração personalizada para síntese de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: texto para a fala
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428193"
---
# <a name="get-started-with-text-to-speech"></a>Introdução à conversão de texto em voz

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Obtenha informações sobre posição

O seu projeto pode precisar de saber quando uma palavra é falada por discurso a texto para que possa tomar medidas específicas com base nesse timing. Como exemplo, se quisesse destacar as palavras à medida que foram ditas, precisaria de saber o que destacar, quando realçá-la e por quanto tempo a destacar.

Pode fazê-lo utilizando o `WordBoundary` evento disponível dentro `SpeechSynthesizer` de . Este evento é levantado no início de cada nova palavra falada e fornecerá uma compensação de tempo dentro do fluxo falado, bem como um desvio de texto dentro do pedido de entrada.

* `AudioOffset` relata o tempo decorrido do áudio de saída entre o início da síntese e o início da próxima palavra. Isto é medido em unidades de cem nanossegundos (HNS) com 10.000 HNS equivalentes a 1 milissegundo.
* `WordOffset` relata a posição do carácter na cadeia de entrada (texto original ou [SSML)](speech-synthesis-markup.md)imediatamente antes da palavra que está prestes a ser falada.

> [!NOTE]
> `WordBoundary` os eventos são levantados à medida que os dados de áudio de saída ficam disponíveis, o que será mais rápido do que a reprodução de um dispositivo de saída. A sincronização adequada do tempo de streaming para "tempo real" deve ser realizada pelo chamador.

Pode encontrar exemplos de `WordBoundary` utilização nas [amostras de texto-a-discurso](https://aka.ms/csspeech/samples) no GitHub.

## <a name="next-steps"></a>Passos seguintes

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Melhorar a sintetização com SSML](speech-synthesis-markup.md)
* Saiba como usar a [API de Áudio Longo](long-audio-api.md) para grandes amostras de texto, como livros e artigos de notícias
* Veja as [amostras de arranque rápido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) no GitHub
