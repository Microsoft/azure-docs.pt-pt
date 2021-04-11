---
title: Visão geral da tradução da fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A tradução da fala permite-lhe adicionar tradução de voz em tempo real, em tempo real, multi-linguístico às suas aplicações, ferramentas e dispositivos. A mesma API pode ser utilizada para a tradução de voz para voz e para a tradução de voz para texto. Este artigo é uma visão geral dos benefícios e capacidades do serviço de tradução da fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: tradução da fala
ms.openlocfilehash: 99541d7fe9eaa867860af93bc1423d476ce8bf4a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449941"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de voz?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você aprende sobre os benefícios e capacidades do serviço de tradução da fala, que permite a tradução em tempo real, [multi-línguas fala-a-fala](language-support.md#speech-translation) e tradução de voz-para-texto de streams de áudio. Com o Speech SDK, as suas aplicações, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados provisórios de transcrição e tradução são devolvidos à medida que a fala é detetada, e os resultados finais podem ser convertidos em discurso sintetizado.

Esta documentação contém os seguintes tipos de artigos:

* **Os quickstarts** estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.
* **Os guias de como fazer** contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.
* **Os conceitos** fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.
* **Os tutoriais** são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.

## <a name="core-features"></a>Características do núcleo

* Tradução de voz para texto com resultados de reconhecimento.
* Tradução fala-a-fala.
* Suporte para tradução para várias línguas-alvo.
* Resultados provisórios de reconhecimento e tradução.

## <a name="get-started"></a>Introdução 

Veja o [início rápido](get-started-speech-translation.md) para começar com a tradução da fala. O serviço de tradução de voz está disponível através do [Speech SDK](speech-sdk.md) e do [Speech CLI](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

O código de amostra para o SDK de discurso está disponível no GitHub. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento/tradução contínuo e único, e trabalhar com modelos personalizados.

* [Amostras de linguagem e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se as suas aplicações, ferramentas ou produtos estiverem a utilizar a [API do Discurso](./how-to-migrate-from-translator-speech-api.md)do Tradutor, criámos guias para o ajudar a migrar para o serviço Desempiração.

* [Migrar da API do Discurso do Tradutor para o serviço de Discurso](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de referência

* [API de Voz](./speech-sdk.md)
* [SDK de Dispositivos de Voz](speech-devices-sdk.md)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-discurso](rest-text-to-speech.md)
* [REST API: Transcrição e personalização do lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Passos seguintes

* Complete o [quickstart](get-started-speech-translation.md) de tradução da fala
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Obtenha o Discurso SDK](speech-sdk.md)