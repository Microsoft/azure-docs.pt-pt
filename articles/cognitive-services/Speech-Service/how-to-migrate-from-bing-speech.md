---
title: Migrar do serviço Bing Speech to Speech
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma subscrição de Bing Speech existente para o serviço de fala da Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d6b7b298e7e4d18e68985ec3cf536c90ef773608
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644385"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar do Discurso de Bing para o serviço de fala

Utilize este artigo para migrar as suas aplicações da API do Discurso de Bing para o serviço de Fala.

Este artigo descreve as diferenças entre as APIs do Discurso de Bing e o serviço de Discurso, e sugere estratégias para migrar as suas aplicações. A sua chave de subscrição de API de discurso bing não funcionará com o serviço De Discurso; vai precisar de uma nova subscrição do serviço speech.

Uma única chave de subscrição de serviço de discurso dá acesso às seguintes funcionalidades. Cada uma é medida em separado, pelo que apenas lhe são cobradas as funcionalidades que utilizar.

* [Conversão de voz em texto](speech-to-text.md)
* [Conversão de voz em texto personalizada](/azure/cognitive-services/speech-service/custom-speech-overview)
* [Conversão de texto em voz](text-to-speech.md)
* [Vozes personalizadas para conversão de texto em voz](./how-to-custom-voice-create-voice.md)
* [Tradução de Voz](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [Speech SDK](speech-sdk.md) é um substituto funcional para as bibliotecas de clientes Bing Speech, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de características

O serviço de Discurso é em grande parte semelhante ao Discurso de Bing, com as seguintes diferenças.

| Funcionalidade | Voz do Bing | Serviço de voz | Detalhes |
|--|--|--|--|
| SDK C# | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala suporta o Windows 10, a Universal Windows Platform (UWP) e o .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | O serviço de fala suporta o Windows e o Linux. |
| SDK Java | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala suporta dispositivos Android e Speech. |
| Reconhecimento contínuo da fala | 10 minutos | Ilimitado | O SDK de voz suporta o reconhecimento contínuo ilimitado e reconecta-se automaticamente após o intervalo ou desconexão. |
| Resultados parciais ou provisórios | :heavy_check_mark: | :heavy_check_mark: | Apoiado com o Discurso SDK. |
| Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | Bing Speech requer uma subscrição separada do Discurso Personalizado. |
| Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | Bing Speech requer uma subscrição de Voz Personalizada separada. |
| Vozes de 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Reconhecimento de intenções de fala | Requer chamada separada da API LUIS | Integrado (com SDK) | Pode utilizar uma chave LUIS com o serviço de Discurso. |
| Reconhecimento de intenções simples | :heavy_minus_sign: | :heavy_check_mark: |
| Transcrição de lote de longos ficheiros áudio | :heavy_minus_sign: | :heavy_check_mark: |
| Modo de reconhecimento | Manual via endpoint URI | Automático | O modo de reconhecimento não está disponível no serviço Discurso. |
| Localidade do ponto final | Global | Regional | Os pontos finais regionais melhoram a latência. |
| APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs do serviço de fala REST são compatíveis com a Bing Speech (ponto final diferente). As APIs de repouso suportam a funcionalidade texto-a-fala e a funcionalidade limitada de fala-a-texto. |
| Protocolos WebSockets | :heavy_check_mark: | :heavy_minus_sign: | O Speech SDK abstrata as ligações web para funcionalidade que requer uma ligação constante ao serviço, pelo que já não existe suporte para as subscrever manualmente. |
| Chamadas de API de serviço para serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecido em Bing Speech através da Biblioteca de Serviços C. |
| SDK de código aberto | :heavy_check_mark: | :heavy_minus_sign: |

O serviço Speech utiliza um modelo de preços baseado no tempo (em vez de um modelo baseado em transações). Consulte [os preços do serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para mais detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou a sua organização tiver aplicações em desenvolvimento ou produção que utilizem uma API de Discurso Bing, deve atualizá-las para usar o serviço Desempiração o mais rapidamente possível. Consulte a [documentação](index.yml) do serviço de discurso para SDKs disponíveis, amostras de código e tutoriais.

As [APIs](./overview.md#reference-docs) do serviço de voz REST são compatíveis com as APIs de discurso de Bing. Se estiver a utilizar as APIs de DESCANSO de Discurso de Bing, só precisa de alterar o ponto final REST e mudar para uma chave de subscrição de serviço de discurso.

Se estiver a utilizar uma biblioteca de clientes Bing Speech para uma linguagem de programação específica, migrar para o [SDK do Discurso](speech-sdk.md) requer alterações na sua aplicação, porque a API é diferente. O Speech SDK pode tornar o seu código mais simples, ao mesmo tempo que lhe dá acesso a novas funcionalidades. O Speech SDK está disponível numa grande variedade de linguagens de programação. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento de várias plataformas.

O serviço de discurso não oferece um ponto final global. Determine se a sua aplicação funciona de forma eficiente quando utilizar um único ponto de terminamento regional para todo o seu tráfego. Caso contrário, utilize a geolocalização para determinar o ponto final mais eficiente. Precisa de uma subscrição separada do serviço de discurso em cada região que utilizar.

Para começar com o Discurso SDK:

1. Descarregue o [Speech SDK](speech-sdk.md).
1. Trabalhe através dos guias e [tutoriais](how-to-recognize-intents-from-speech-csharp.md)do serviço [speech.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) Consulte também as [amostras](./speech-sdk.md#sample-source-code) de código para obter experiência com as novas APIs.
1. Atualize a sua aplicação para utilizar o serviço Desemaçamento.

## <a name="support"></a>Suporte

Os clientes da Bing Speech devem contactar o apoio ao cliente abrindo um [bilhete de apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Também pode contactar-nos caso a sua necessidade de suporte exija um [plano de suporte técnico.](https://azure.microsoft.com/support/plans/)

Para o serviço de fala, suporte SDK e API, visite a [página de suporte](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)do serviço speech .

## <a name="next-steps"></a>Passos seguintes

* [Experimente o serviço de fala gratuitamente](overview.md#try-the-speech-service-for-free)
* [Introdução à conversão de voz em texto](get-started-speech-to-text.md)
* [Introdução à conversão de texto em voz](get-started-text-to-speech.md)

## <a name="see-also"></a>Ver também

* [Notas de lançamento do serviço de fala](releasenotes.md)
* [O que é o serviço de fala](overview.md)
* [Serviço de fala e documentação SDK de fala](speech-sdk.md#get-the-speech-sdk)
