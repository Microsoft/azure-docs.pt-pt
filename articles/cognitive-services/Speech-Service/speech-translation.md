---
title: Tradução de fala com o serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala permite que você adicione tradução de fala de ponta a ponta, em tempo real e em vários idiomas, a seus aplicativos, ferramentas e dispositivos. A mesma API pode ser utilizada para a tradução de voz para voz e conversão de voz em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a05a2bf81a278322bc4e07ed959aedb828c39b73
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815472"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de voz?

A tradução de fala do serviço de fala permite a tradução de fala em tempo real, de voz a fala e de fala a texto de fluxos de áudio. Com o SDK de fala, seus aplicativos, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados provisórios de transcrição e tradução são retornados conforme a fala é detectada e os resultados de finais podem ser convertidos em fala sintetizada.

O mecanismo de tradução da Microsoft é fornecido por duas abordagens diferentes: conversão de máquina estatística (SMT) e conversão de máquina neural (NMT). O SMT usa análise estatística avançada para estimar a melhor tradução possível, dado o contexto de algumas palavras. Com o NMT, as redes neurais são usadas para fornecer traduções mais precisas e de som natural usando o contexto completo de frases para traduzir palavras.

Hoje, a Microsoft usa o NMT para tradução para as linguagens mais populares. Todos os [idiomas disponíveis para a tradução de voz para voz](language-support.md#speech-translation) têm a tecnologia NMT. Tradução de voz em texto utilizar SMT ou NMT consoante o par de idioma. Quando o idioma de destino tem suporte do NMT, a tradução completa é NMT. Quando o idioma de destino não tem suporte do NMT, a tradução é um híbrido de NMT e SMT, usando o inglês como uma "dinamização" entre os dois idiomas.

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Tradução de conversão de fala em texto com resultados de reconhecimento. | Sim | Não |
| Conversão de fala em fala. | Sim | Não |
| Resultados de tradução e de reconhecimento provisórios. | Sim | Não |

## <a name="get-started-with-speech-translation"></a>Introdução à tradução de fala

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos. Esta tabela inclui uma lista de guias de início rápido de tradução de fala organizados por idioma.

| Início Rápido | Plataforma | Referência de API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Navegador](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Navegador](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Navegador](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Navegador](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Navegador](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento/conversão de captura única e contínua e o trabalho com modelos personalizados.

* [Exemplos de conversão de fala em texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se seus aplicativos, ferramentas ou produtos estiverem usando o [API de tradução de fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), criamos guias para ajudá-lo a migrar para o serviço de fala.

* [Migrar do API de Tradução de Fala para o serviço de fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [SDK de dispositivos de fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de assinatura do serviço de fala gratuitamente](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
