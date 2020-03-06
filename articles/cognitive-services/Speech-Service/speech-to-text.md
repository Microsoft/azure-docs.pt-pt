---
title: Discurso ao texto - Serviço de discurso
titleSuffix: Azure Cognitive Services
description: A função de fala-a-texto permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas sobre esta entrada de texto. Este serviço funciona perfeitamente com as características de texto-a-fala (síntese de fala) e tradução da fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388290"
---
# <a name="what-is-speech-to-text"></a>O que é a conversão de voz em texto?

O discurso a texto do serviço speech, também conhecido como reconhecimento da fala, permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas sobre este texto como entrada de comando. Este serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft utiliza para produtos Cortana e Office. Funciona perfeitamente com as ofertas de serviço de <a href="./speech-translation.md" target="_blank">tradução <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e <a href="./text-to-speech.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> texto-para-fala.</a> Para obter uma lista completa das línguas de fala a texto disponíveis, consulte [as línguas apoiadas.](language-support.md#speech-to-text)

O serviço de fala-a-texto não se aplica à utilização do modelo de linguagem Universal. Este modelo foi treinado utilizando dados da Microsoft e está implantado na nuvem. É ideal para cenários de conversação e ditado. Ao usar o discurso ao texto para reconhecimento e transcrição num ambiente único, pode criar e treinar modelos acústicos, linguísticos e pronúncia personalizados. A personalização é útil para abordar o ruído ambiente ou o vocabulário específico da indústria.

> [!NOTE]
> Bing Speech foi desativado a 15 de outubro de 2019. Se as suas aplicações, ferramentas ou produtos estiverem usando as APIs ou Discurso Personalizado do Bing Speech, criamos guias para ajudá-lo a migrar para o serviço de Fala.
> - [Migrar do Discurso de Bing para o serviço de discurso](how-to-migrate-from-bing-speech.md)
> - [Migrar do discurso personalizado para o serviço de discurso](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Começar com o discurso-a-texto

O serviço de fala-a-texto está disponível através do [Speech SDK](speech-sdk.md). Existem vários cenários comuns disponíveis como quickstarts, em vários idiomas e plataformas:

 - [Quickstart: Reconhecer o discurso com entrada no microfone](quickstarts/speech-to-text-from-microphone.md)
 - [Quickstart: Reconhecer o discurso de um ficheiro](quickstarts/speech-to-text-from-file.md)
 - [Quickstart: Reconhecer discurso armazenado no armazenamento de blob](quickstarts/from-blob.md)

Se preferir utilizar o serviço de DESCANSO da fala-a-texto, consulte [ASAP](rest-speech-to-text.md)IS REST .

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de amostra

Depois de ter tido a oportunidade de usar o serviço de Fala, experimente o nosso tutorial que o ensina a reconhecer as intenções da fala usando o SDK da Fala e o LUIS.

- [Tutorial: Reconhecer as intenções do discurso com o SDK de Fala e LUIS, usandoC#](how-to-recognize-intents-from-speech-csharp.md)

O código de amostra para o SDK do Discurso está disponível no GitHub. Estas amostras cobrem cenários comuns como ler áudio de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados.

- [Amostras de discurso a texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo padrão de serviço da Fala, pode criar modelos personalizados. A personalização ajuda a superar barreiras de reconhecimento de voz, como estilo de fala, vocabulário e ruído de fundo, ver [Discurso Personalizado](how-to-custom-speech.md). As opções de personalização variam de acordo com a linguagem/local, consulte [idiomas suportados](supported-languages.md) para verificar o suporte.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
- [Obtenha o SDK do Discurso](speech-sdk.md)
