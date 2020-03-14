---
title: Texto-a-voz - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A funcionalidade de texto-a-fala no serviço Da fala permite que as suas aplicações, ferramentas ou dispositivos convertam texto em discurso sintetizado natural como o humano. Escolha vozes predefinidas ou crie a sua própria voz personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f939619cbbc69e9fc9cdbc4b2085a0b9d805511d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371210"
---
# <a name="what-is-text-to-speech"></a>O que é a conversão de texto em voz?

O texto-a-voz do serviço speech permite que as suas aplicações, ferramentas ou dispositivos convertam texto em discurso sintetizado semelhante ao humano. Escolha entre vozes padrão e neurais, ou crie uma voz personalizada única para o seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, e 5 vozes neurais estão disponíveis em um número selecionado de idiomas e locais. Para uma lista completa de vozes, línguas e locais apoiados, consulte [línguas apoiadas.](language-support.md#text-to-speech)

> [!NOTE]
> Bing Speech foi desativado a 15 de outubro de 2019. Se as suas aplicações, ferramentas ou produtos estiverem usando as APIs ou Discurso Personalizado do Bing Speech, criamos guias para ajudá-lo a migrar para o serviço de Fala.
> - [Migrar do Discurso de Bing para o serviço de discurso](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Características do núcleo 

* Síntese da fala - Use o [SDK](quickstarts/text-to-speech-audio-file.md) de Fala ou [a API DO REST](rest-text-to-speech.md) para converter texto-a-fala usando vozes padrão, neurais ou personalizadas.

* Síntese assíncrona de áudio longo - Utilize a [API](long-audio-api.md) de áudio longo para sincronizar sincronicamente ficheiros de texto-a-fala superiores a 10 minutos (por exemplo, livros de áudio ou palestras). Ao contrário da síntese realizada usando o SDK do Discurso ou a API do REST de fala-a-texto, as respostas não são devolvidas em tempo real. A expectativa é que os pedidos sejam enviados assincronicamente, as respostas são inquiridas e que o áudio sintetizado é descarregado quando disponibilizado a partir do serviço. Apenas vozes neurais são apoiadas.

* Vozes padrão - Criadas utilizando técnicas de síntese paramétrica estatística e/ou síntese de concatenação. Estas vozes são altamente inteligíveis e sãs naturais. Pode facilmente permitir que as suas aplicações falem em mais de 45 línguas, com uma ampla gama de opções de voz. Estas vozes fornecem uma elevada precisão de pronúncia, incluindo suporte para abreviaturas, expansões de siglas, interpretações data/hora, polífos e muito mais. Para obter uma lista completa de vozes padrão, consulte [línguas apoiadas](language-support.md#text-to-speech).

* Vozes neurais - Redes neuronais profundas são usadas para ultrapassar os limites da síntese tradicional da fala no que diz respeito ao stress e entoação na língua falada. A previsão da prosody e a síntese de voz são realizadas simultaneamente, o que resulta em saídas mais fluidas e naturais. As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como livros eletrónicos em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando interagecom sistemas de IA. Para obter uma lista completa de vozes neurais, consulte [línguas apoiadas.](language-support.md#text-to-speech)

* Linguagem de marcação da síntese da fala (SSML) - Uma linguagem de marcação baseada em XML usada para personalizar saídas de fala-a-texto. Com o SSML, pode ajustar o pitch, adicionar pausas, melhorar a pronúncia, acelerar ou abrandar a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um único documento. Ver [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Introdução

O serviço de texto-a-fala está disponível através do [SDK](speech-sdk.md)do Discurso . Existem vários cenários comuns disponíveis como quickstarts, em vários idiomas e plataformas:

* [Sintetizar o discurso num ficheiro áudio](quickstarts/text-to-speech-audio-file.md)
* [Sintetizar o discurso a um orador](quickstarts/text-to-speech.md)
* [Sincronicamente sintetizam áudio de forma longa](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Se preferir, o serviço de texto à fala é acessível via [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Código de exemplo

O código de amostra para texto-a-fala está disponível no GitHub. Estas amostras cobrem a conversão texto-a-fala nas línguas de programação mais populares.

- [Amostras de texto para a fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de texto para a fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização 

Além de vozes standard e neurais, pode criar e afinar vozes personalizadas exclusivas do seu produto ou marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Para mais informações, consulte [Começar com Voz Personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota de preços

Ao utilizar o serviço de texto para a fala, é cobrado por cada personagem que é convertido em discurso, incluindo pontuação. Embora o documento SSML em si não seja faturado, os elementos opcionais que são usados para ajustar a forma como o texto é convertido para a fala, como fonés e pitch, são contados como caracteres de billable. Aqui está uma lista do que é cobrado:

- Texto transmitido para o serviço de texto-a-fala no corpo SSML do pedido
- Todas as marcações no campo de texto do organismo de pedido no formato SSML, com exceção das etiquetas `<speak>` e `<voice>`
- Letras, pontuação, espaços, separadores, marcação, e todos os caracteres do espaço branco
- Cada ponto de código definido no Unicode

Para obter informações detalhadas, consulte [O Preço](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada personagem de língua chinesa, japonesa e coreana é contada como dois caracteres para faturação.

## <a name="reference-docs"></a>Doutorados de referência

- [SDK de Voz](speech-sdk.md)
- [REST API: Texto-a-falar](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma subscrição gratuita do serviço De fala](get-started.md)
- [Obtenha o SDK do Discurso](speech-sdk.md)
