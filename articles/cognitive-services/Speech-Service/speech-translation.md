---
title: Tradução da fala com serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala permite-lhe adicionar tradução de fala em tempo real, em tempo real, multilingues para as suas aplicações, ferramentas e dispositivos. A mesma API pode ser utilizada para a tradução de voz para voz e para a tradução de voz para texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052626"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de voz?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A tradução da fala do serviço Speech permite a tradução em tempo real, multi-linguagem da fala à fala e da tradução fala-a-texto dos streams de áudio. Com o SDK de Fala, as suas aplicações, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados provisórios da transcrição e tradução são devolvidos à medida que a fala é detetada, e os resultados finais podem ser convertidos em discurso sintetizado.

O motor de tradução da Microsoft é alimentado por duas abordagens diferentes: tradução de máquinas estatísticas (SMT) e tradução de máquinas neurais (NMT). O SMT utiliza análiseestatística avançada para estimar as melhores traduções possíveis dado o contexto de algumas palavras. Com o NMT, as redes neurais são usadas para fornecer traduções mais precisas e naturais, utilizando todo o contexto das frases para traduzir palavras.

Hoje em dia, a Microsoft usa NMT para tradução para os idiomas mais populares. Todas as [línguas disponíveis para tradução fala-a-fala](language-support.md#speech-translation) são alimentadas por NMT. A tradução fala-a-texto pode utilizar SMT ou NMT dependendo do par de idiomas. Quando a linguagem-alvo é suportada por NMT, a tradução completa é movida por NMT. Quando a linguagem-alvo não é suportada pelo NMT, a tradução é um híbrido de NMT e SMT, usando o inglês como um "pivô" entre as duas línguas.

## <a name="core-features"></a>Características do núcleo

Aqui estão as funcionalidades disponíveis através do Speech SDK e DOP REST:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Tradução fala-a-texto com resultados de reconhecimento. | Sim | Não |
| Tradução fala-a-fala. | Sim | Não |
| Resultados provisórios de reconhecimento e tradução. | Sim | Não |

## <a name="get-started-with-speech-translation"></a>Começar com a tradução da fala

Oferecemos quickstarts projetados para tê-lo em funcionamento código em menos de 10 minutos. Esta tabela inclui uma lista de quickstarts de tradução da fala organizados pela linguagem.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Procurar](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de amostra para o SDK do Discurso está disponível no GitHub. Estas amostras cobrem cenários comuns como ler áudio de um ficheiro ou stream, reconhecimento/tradução contínuo e de tiro único, e trabalhar com modelos personalizados.

* [Amostras de fala a texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se as suas aplicações, ferramentas ou produtos estiverem a usar a API do Discurso do [Tradutor,](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)criámos guias para o ajudar a migrar para o serviço de Fala.

* [Migrar da API do Discurso tradutor para o serviço de discurso](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Doutorados de referência

* [API de Voz](speech-sdk-reference.md)
* [SDK de Dispositivos de Voz](speech-devices-sdk.md)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-falar](rest-text-to-speech.md)
* [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Obtenha o SDK do Discurso](speech-sdk.md)
