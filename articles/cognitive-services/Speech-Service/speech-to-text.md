---
title: Visão geral do discurso para o texto - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O software de fala para texto permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas nesta entrada de texto. Este artigo é uma visão geral dos benefícios e capacidades do serviço de voz a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: discurso ao texto, discurso ao software de texto
ms.openlocfilehash: 3450d39729096bfc3077f51e2069f8f102e571a5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449397"
---
# <a name="what-is-speech-to-text"></a>O que é a conversão de voz em texto?

Nesta visão geral, você aprende sobre os benefícios e capacidades do serviço de voz a texto.
O discurso-a-texto, também conhecido como reconhecimento de voz, permite a transcrição em tempo real de streams de áudio em texto. As suas aplicações, ferramentas ou dispositivos podem consumir, exibir e tomar medidas neste texto como entrada de comando. Este serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft utiliza para produtos Cortana e Office. Funciona perfeitamente com as ofertas de serviços de <a href="./speech-translation.md" target="_blank">tradução</a> e <a href="./text-to-speech.md" target="_blank">texto-a-fala.</a> Para obter uma lista completa das línguas disponíveis de voz para texto, consulte [as línguas apoiadas.](language-support.md#speech-to-text)

O serviço de voz para texto não utiliza o modelo de linguagem Universal. Este modelo foi treinado com dados da Microsoft e é implementado na nuvem. É ideal para cenários de conversação e ditado. Ao utilizar modelos de voz para texto para reconhecimento e transcrição num ambiente único, pode criar e treinar modelos acústicos, linguísticos e de pronúncia personalizados. A personalização é útil para abordar o ruído ambiente ou o vocabulário específico da indústria.

Esta documentação contém os seguintes tipos de artigos:

* **Os quickstarts** estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.
* **Os guias de como fazer** contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.
* **Os conceitos** fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.
* **Os tutoriais** são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.

> [!NOTE]
> Bing Speech foi desativado a 15 de outubro de 2019. Se as suas aplicações, ferramentas ou produtos estiverem a utilizar as APIs do Discurso de Bing, criámos guias para o ajudar a migrar para o serviço Desemaçamento.
> - [Migrar do Discurso de Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Introdução

Veja o [início rápido](get-started-speech-to-text.md) para começar com discurso-a-texto. O serviço está disponível através do [Speech SDK,](speech-sdk.md)da [API REST](rest-speech-to-text.md#pronunciation-assessment-parameters)e do [Speech CLI](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

O código de amostra para o SDK de discurso está disponível no GitHub. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados.

- [Amostras de palavras para texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de avaliação da pronúncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalização

Além do modelo padrão de serviço de fala, pode criar modelos personalizados. A personalização ajuda a superar barreiras de reconhecimento de voz, tais como estilo de fala, vocabulário e ruído de fundo, ver [Discurso Personalizado.](./custom-speech-overview.md) As opções de personalização variam de acordo com o idioma/local, consulte [idiomas suportados](./language-support.md) para verificar o suporte.

## <a name="batch-transcription"></a>Transcrição em lote

A transcrição do lote é um conjunto de operações de API REST que permitem transcrever uma grande quantidade de áudio no armazenamento. Pode apontar para ficheiros de áudio com um URI de assinatura de acesso partilhado (SAS) e receber os resultados de transcrição de forma assíncrona. Consulte o [como fazer para](batch-transcription.md) obter mais informações sobre como usar a API de transcrição do lote.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Passos seguintes

- [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
- [Obtenha o Discurso SDK](speech-sdk.md)