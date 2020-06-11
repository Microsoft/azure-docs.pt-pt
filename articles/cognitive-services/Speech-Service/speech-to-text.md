---
title: Discurso-a-texto - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A funcionalidade discurso-texto permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas nesta entrada de texto. Este serviço funciona perfeitamente com o texto-a-voz (síntese da fala) e as características de tradução da fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: b1e84f9acb439324317ed2f0d11ed06eb3e43ad8
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678074"
---
# <a name="what-is-speech-to-text"></a>O que é a conversão de voz em texto?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O discurso-a-texto do serviço de discurso, também conhecido como reconhecimento de voz, permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas neste texto como entrada de comando. Este serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft utiliza para produtos Cortana e Office. Funciona perfeitamente com as ofertas de serviços de <a href="./speech-translation.md" target="_blank">tradução <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e <a href="./text-to-speech.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> texto-a-fala.</a> Para obter uma lista completa das línguas disponíveis de voz para texto, consulte [as línguas apoiadas.](language-support.md#speech-to-text)

O serviço de voz para texto não utiliza o modelo de linguagem Universal. Este modelo foi treinado com dados da Microsoft e é implementado na nuvem. É ideal para cenários de conversação e ditado. Ao utilizar modelos de voz para texto para reconhecimento e transcrição num ambiente único, pode criar e treinar modelos acústicos, linguísticos e de pronúncia personalizados. A personalização é útil para abordar o ruído ambiente ou o vocabulário específico da indústria.

Com um texto de referência adicional como entrada, o serviço de voz a texto também permite a capacidade de avaliação da [pronúncia](rest-speech-to-text.md#pronunciation-assessment-parameters) da fala para avaliar a pronúncia da fala e dá feedback aos oradores sobre a precisão e fluência do áudio falado. Com a avaliação da pronúncia, os alunos de línguas podem praticar, obter feedback instantâneo e melhorar a sua pronúncia para que possam falar e apresentar com confiança. Os educadores podem usar a capacidade de avaliar a pronúncia de vários falantes em tempo real. A funcionalidade suporta atualmente o inglês dos EUA e está muito relacionada com avaliações de fala realizadas por especialistas.

> [!NOTE]
> Bing Speech foi desativado a 15 de outubro de 2019. Se as suas aplicações, ferramentas ou produtos estiverem a utilizar as APIs do Discurso de Bing, criámos guias para o ajudar a migrar para o serviço Desemaçamento.
> - [Migrar do Discurso de Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Começar com discurso-a-texto

O serviço de voz a texto está disponível através do [Speech SDK](speech-sdk.md). Existem vários cenários comuns disponíveis como quickstarts, em vários idiomas e plataformas:

 - [Quickstart: Reconhecer a fala com a entrada do microfone](quickstarts/speech-to-text-from-microphone.md)
 - [Quickstart: Reconhecer a fala de um ficheiro](quickstarts/speech-to-text-from-file.md)
 - [Quickstart: Reconhecer a fala armazenada no armazenamento de bolhas](quickstarts/from-blob.md)

Se preferir utilizar o serviço REST de voz a texto, consulte [AS APIs de REST](rest-speech-to-text.md).

 - [Quickstart: Avaliação de pronúncia com entrada de referência](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de amostra

Depois de ter tido a oportunidade de usar o serviço de Discurso, experimente o nosso tutorial que lhe ensina a reconhecer as intenções da fala usando o Discurso SDK e o LUIS.

- [Tutorial: Reconhecer intenções do discurso com o Discurso SDK e LUIS, usando C #](how-to-recognize-intents-from-speech-csharp.md)

O código de amostra para o SDK de discurso está disponível no GitHub. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados.

- [Amostras de palavras para texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de avaliação da pronúncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalização

Além do modelo padrão de serviço de fala, pode criar modelos personalizados. A personalização ajuda a superar barreiras de reconhecimento de voz, tais como estilo de fala, vocabulário e ruído de fundo, ver [Discurso Personalizado.](how-to-custom-speech.md) As opções de personalização variam de acordo com o idioma/local, consulte [idiomas suportados](supported-languages.md) para verificar o suporte.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Próximos passos

- [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](get-started.md)
- [Obtenha o Discurso SDK](speech-sdk.md)
