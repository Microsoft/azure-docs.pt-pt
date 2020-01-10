---
title: Conversão de texto em fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de texto em fala no serviço de fala permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada semelhante à humana. Escolha vozes predefinidas ou crie sua própria voz personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: erhopf
ms.openlocfilehash: d076d2520bda4d6209fbdd991df57c4eedaa3938
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379883"
---
# <a name="what-is-text-to-speech"></a>O que é a conversão de texto em voz?

A conversão de texto em fala do serviço de fala permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada por meio do tipo humano. Escolha entre vozes padrão e neural ou crie uma voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em um número selecionado de idiomas e localidades. Para obter uma lista completa de vozes, idiomas e localidades com suporte, consulte [idiomas com suporte](language-support.md#text-to-speech).

> [!NOTE]
> Fala do Bing foi encerrado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando o Fala do Bing APIs ou Fala Personalizada, criamos guias para ajudá-lo a migrar para o serviço de fala.
> - [Migrar do Fala do Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Principais recursos 

* Síntese de fala – use o [SDK de fala](quickstarts/text-to-speech-audio-file.md) ou a [API REST](rest-text-to-speech.md) para converter conversão de texto em fala usando vozes padrão, neural ou personalizadas.

* Síntese assíncrona de longo áudio – Use a [API de áudio longo](long-audio-api.md) para sintetizar de forma assíncrona arquivos de texto para fala por mais de 10 minutos (por exemplo, livros de áudio ou palestras). Ao contrário da síntese executada usando o SDK de fala ou a API REST de fala em texto, as respostas não são retornadas em tempo real. A expectativa é que as solicitações sejam enviadas de forma assíncrona, que as respostas sejam sondadas e que o áudio sintetizado seja baixado quando disponibilizado a partir do serviço. Somente as vozes neurais têm suporte.

* Vozes padrão-criadas usando as técnicas de síntese paramétricas estatística e/ou síntese de concatenação. Essas vozes são altamente inteligível e sólidas. Você pode facilmente permitir que seus aplicativos falem em mais de 45 idiomas, com uma ampla gama de opções de voz. Essas vozes fornecem alta precisão de pronúncia, incluindo suporte para abreviações, expansões de acrônimo, interpretações de data/hora, telefones e muito mais. Para obter uma lista completa de vozes padrão, consulte [idiomas com suporte](language-support.md#text-to-speech).

* Vozes neurais-as redes neurais profundas são usadas para superar os limites da síntese de fala tradicional com relação a estresse e intonation na linguagem falada. A previsão de Prosody e a síntese de voz são executadas simultaneamente, o que resulta em saídas mais suaves e de som natural. As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos em Audiobooks, e aprimorar os sistemas de navegação no carro. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando você interage com sistemas de ia. Para obter uma lista completa de vozes neurais, consulte [idiomas com suporte](language-support.md#text-to-speech).

* Linguagem de marcação de síntese de fala (SSML) – uma linguagem de marcação baseada em XML usada para personalizar saídas de fala para texto. Com o SSML, você pode ajustar o timbre, adicionar pausas, melhorar a pronúncia, acelerar ou diminuir a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um único documento. Consulte [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Começar

O serviço de conversão de texto em fala está disponível por meio do [SDK de fala](speech-sdk.md). Há vários cenários comuns disponíveis como guias de início rápido, em várias linguagens e plataformas:

* [Sintetizar a fala em um arquivo de áudio](quickstarts/text-to-speech-audio-file.md)
* [Sintetizar a fala para um palestrante](quickstarts/text-to-speech.md)
* [Sintetizar de forma assíncrona o áudio de formato longo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Se preferir, o serviço de conversão de texto em fala pode ser acessado via [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para conversão de texto em fala está disponível no GitHub. Esses exemplos abordam a conversão de texto em fala nas linguagens de programação mais populares.

- [Exemplos de conversão de texto em fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização 

Além das vozes padrão e neural, você pode criar e ajustar vozes personalizadas exclusivas para seu produto ou marca. Tudo o que é necessário para começar são alguns arquivos de áudio e as transcrições associadas. Para obter mais informações, consulte Introdução [à voz personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota de preços

Ao usar o serviço de conversão de texto em fala, você será cobrado por cada caractere convertido em fala, incluindo pontuação. Embora o documento SSML em si não seja Faturável, elementos opcionais usados para ajustar como o texto é convertido em fala, como fonemas e pitch, são contados como caracteres faturáveis. Aqui está uma lista do que é Faturável:

- Texto passado para o serviço de conversão de texto em fala no corpo de SSML da solicitação
- Toda a marcação dentro do campo de texto do corpo da solicitação no formato SSML, com exceção das marcas `<speak>` e `<voice>`
- Letras, pontuação, espaços, tabulações, marcação e todos os caracteres de espaço em branco
- Todos os pontos de código definidos em Unicode

Para obter informações detalhadas, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada caractere de idioma chinês, japonês e coreano é contado como dois caracteres para cobrança.

## <a name="reference-docs"></a>Documentos de referência

- [SDK de Voz](speech-sdk.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma assinatura gratuita do serviço de fala](get-started.md)
- [Obtenha o SDK de fala](speech-sdk.md)
