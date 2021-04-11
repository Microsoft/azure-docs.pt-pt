---
title: Visão geral text-to-speech - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A funcionalidade text-to-speech no serviço Discurso permite que as suas aplicações, ferramentas ou dispositivos convertam o texto em linguagem humana natural. Este artigo é uma visão geral dos benefícios e capacidades do serviço text-to-speech.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: texto para a fala
ms.openlocfilehash: 1a7ff7befc1a5e82c5518cf103a81b35ec38c7db
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449363"
---
# <a name="what-is-text-to-speech"></a>O que é a conversão de texto em voz?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você aprende sobre os benefícios e capacidades do serviço de texto-a-fala, que permite que suas aplicações, ferramentas ou dispositivos convertam texto em linguagem humana. Escolha entre vozes padrão e neurais ou crie uma voz personalizada única ao seu produto ou marca. Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, e 5 vozes neurais estão disponíveis em um número selecionado de idiomas e locais. Para obter uma lista completa de vozes, línguas e locais apoiados, consulte [as línguas apoiadas.](language-support.md#text-to-speech)

Esta documentação contém os seguintes tipos de artigos:

* **Os quickstarts** estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.
* **Os guias de como fazer** contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.
* **Os conceitos** fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.
* **Os tutoriais** são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.

> [!NOTE]
> Bing Speech foi desativado a 15 de outubro de 2019. Se as suas aplicações, ferramentas ou produtos estiverem a utilizar as APIs do Discurso Bing ou o Discurso Personalizado, criámos guias para o ajudar a migrar para o serviço Desemaguiso.
> - [Migrar do Discurso de Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Características do núcleo

* Síntese de fala - Utilize a [API](rest-text-to-speech.md) [de Discurso SDK](./get-started-text-to-speech.md) ou REST para converter texto-a-voz usando vozes padrão, neurais ou personalizadas.

* Síntese assíncrono de áudio longo - Utilize a [API de áudio longo](long-audio-api.md) para sintetizar assíncrono ficheiros de texto para voz com mais de 10 minutos (por exemplo, livros áudio ou palestras). Ao contrário da síntese realizada utilizando o Speech SDK ou a API rest de expressão-para-texto, as respostas não são devolvidas em tempo real. A expectativa é que os pedidos sejam enviados assíncronos, as respostas são sondadas e que o áudio sintetizado seja descarregado quando disponibilizado a partir do serviço. Apenas vozes neurais personalizadas são suportadas.

* Vozes padrão - Criado utilizando técnicas de síntese paramétrica estatística e/ou concatenação. Estas vozes são altamente inteligíveis e são naturais. Pode facilmente ativar as suas aplicações para falar em mais de 45 idiomas, com um vasto leque de opções de voz. Estas vozes proporcionam uma elevada precisão de pronúncia, incluindo suporte para abreviaturas, expansões de sigla, interpretações de data/hora, polifones e muito mais. Para obter uma lista completa de vozes padrão, consulte [as línguas suportadas.](language-support.md#text-to-speech)

* Vozes neurais - Redes neuronais profundas são usadas para ultrapassar os limites da síntese tradicional da fala no que diz respeito ao stress e entoação na linguagem falada. A previsão da prosódia e a síntese de voz são realizadas simultaneamente, o que resulta em saídas mais fluidas e de som natural. As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como e-books em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando interage com os sistemas de IA. Para obter uma lista completa de vozes neurais, consulte [as línguas apoiadas.](language-support.md#text-to-speech)

* Ajuste os estilos de fala com SSML - Language de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML usada para personalizar saídas de fala para texto. Com o SSML, pode ajustar o tom, adicionar pausas, melhorar a pronúncia, acelerar ou abrandar a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um único documento. Veja o [como ajustar estilos](speech-synthesis-markup.md) de fala.

* Visemes - [Visemes](how-to-speech-synthesis-viseme.md) são as poses-chave na fala observada, incluindo a posição dos lábios, mandíbula e língua ao produzir um determinado fonme. Os visemes têm uma forte correlação com vozes e fones. Utilizando eventos viseme em Speech SDK, você pode gerar dados de animação facial, que podem ser usados para animar rostos em comunicação de leitura de lábios, educação, entretenimento e atendimento ao cliente.

> [!NOTE]
> Viseme só funciona para `en-US-AriaNeural` a voz por enquanto.

## <a name="get-started"></a>Introdução

Veja o [início rápido](get-started-text-to-speech.md) para começar com texto-a-discurso. O serviço de texto-a-voz está disponível através do [Speech SDK](speech-sdk.md), da [API REST](rest-text-to-speech.md)e do [Speech CLI](spx-overview.md)

## <a name="sample-code"></a>Código de exemplo

O código de amostra para texto-a-voz está disponível no GitHub. Estas amostras cobrem a conversão de texto a discurso nas línguas de programação mais populares.

- [Amostras de texto para falar (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de texto para falar (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização

Além de vozes padrão e neurais, pode criar e afinar vozes personalizadas únicas ao seu produto ou marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Para mais informações, consulte [Começar com a Voz Personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota de preços

Ao utilizar o serviço de texto para voz, é faturado por cada personagem que é convertido em fala, incluindo pontuação. Embora o documento SSML em si não seja faturado, os elementos opcionais que são usados para ajustar a forma como o texto é convertido para a fala, como fonmes e pitch, são contados como caracteres faturados. Aqui está uma lista do que é faturada:

- Texto passado para o serviço de texto-a-fala no organismo SSML do pedido
- Todos os pontos de marcação no campo de texto do organismo de pedido no formato SSML, com exceção `<speak>` das etiquetas e `<voice>` tags
- Letras, pontuação, espaços, separadores, marcação e todos os caracteres do espaço branco
- Todos os pontos de código definidos no Unicode

Para obter informações detalhadas, consulte [a fixação de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

> [!IMPORTANT]
> Cada personagem de língua chinesa, japonesa e coreana é contada como dois caracteres para faturação.

## <a name="reference-docs"></a>Documentos de referência

- [API de Voz](speech-sdk.md)
- [REST API: Texto-a-discurso](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma subscrição de serviço de voz gratuita](overview.md#try-the-speech-service-for-free)
- [Obtenha o Discurso SDK](speech-sdk.md)
