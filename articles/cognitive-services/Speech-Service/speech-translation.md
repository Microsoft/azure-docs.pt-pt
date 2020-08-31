---
title: Tradução da fala com serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala permite-lhe adicionar tradução de voz em tempo real, em tempo real, multi-linguístico às suas aplicações, ferramentas e dispositivos. A mesma API pode ser utilizada para a tradução de voz para voz e para a tradução de voz para texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: cef6ff109607455881970aeb95eaf0859f6f4f05
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055537"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de voz?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A tradução da fala do serviço de fala permite a tradução em tempo real, multi-línguas de fala em voz e de voz para texto de streams de áudio. Com o Speech SDK, as suas aplicações, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados provisórios da transcrição e tradução são devolvidos à medida que a fala é detetada, e os resultados finais podem ser convertidos em discurso sintetizado.

O motor de tradução da Microsoft é alimentado por duas abordagens diferentes: tradução automática estatística (SMT) e tradução de máquinas neurais (NMT). O SMT utiliza uma análise estatística avançada para estimar as melhores traduções possíveis dado o contexto de algumas palavras. Com o NMT, as redes neurais são usadas para fornecer traduções mais precisas e de som natural, utilizando todo o contexto das frases para traduzir palavras.

Hoje em dia, a Microsoft utiliza o NMT para tradução para os idiomas mais populares. Todas as [línguas disponíveis para tradução fala-a-fala](language-support.md#speech-translation) são alimentadas pela NMT. A tradução fala-a-texto pode utilizar SMT ou NMT dependendo do par de idiomas. Quando a língua-alvo é suportada por NMT, a tradução completa é alimentada por NMT. Quando a língua-alvo não é suportada pela NMT, a tradução é um híbrido de NMT e SMT, usando o inglês como um "pivô" entre as duas línguas.

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

Se as suas aplicações, ferramentas ou produtos estiverem a utilizar a [API do Discurso](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)do Tradutor, criámos guias para o ajudar a migrar para o serviço Desempiração.

* [Migrar da API do Discurso do Tradutor para o serviço de Discurso](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de referência

* [API de Voz](speech-sdk-reference.md)
* [SDK de Dispositivos de Voz](speech-devices-sdk.md)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-discurso](rest-text-to-speech.md)
* [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

* Complete o [quickstart](get-started-speech-translation.md) de tradução da fala
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](get-started.md)
* [Obtenha o Discurso SDK](speech-sdk.md)
