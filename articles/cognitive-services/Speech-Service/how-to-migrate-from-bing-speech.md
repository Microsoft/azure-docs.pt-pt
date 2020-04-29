---
title: Migrar do discurso de Bing para o serviço de discurso
titleSuffix: Azure Cognitive Services
description: Aprenda a migrar de uma subscrição existente do Bing Speech para o serviço de Fala dos Serviços Cognitivos Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80656454"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar do Discurso de Bing para o serviço de discurso

Use este artigo para migrar as suas aplicações da API bing speech para o serviço de Discurso.

Este artigo descreve as diferenças entre as APIs do Discurso bing e o serviço de Discurso, e sugere estratégias para migrar as suas aplicações. A sua chave de assinatura Bing Speech API não funcionará com o serviço de Fala; vai precisar de uma nova subscrição do serviço Speech.

Uma única chave de subscrição do serviço Speech dá acesso às seguintes funcionalidades. Cada uma é medida em separado, pelo que apenas lhe são cobradas as funcionalidades que utilizar.

* [Conversão de voz em texto](speech-to-text.md)
* [Conversão de voz em texto personalizada](https://cris.ai)
* [Conversão de texto em voz](text-to-speech.md)
* [Vozes personalizadas para conversão de texto em voz](how-to-customize-voice-font.md)
* [Tradução de Voz](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [Speech SDK](speech-sdk.md) é um substituto funcional para as bibliotecas de clientes bing Speech, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de características

O serviço da Fala é em grande parte semelhante ao Discurso de Bing, com as seguintes diferenças.

| Funcionalidade | Voz do Bing | Serviço de voz | Detalhes |
|--|--|--|--|
| SDK C# | :heavy_check_mark: | :heavy_check_mark: | O serviço de voz suporta o Windows 10, Universal Windows Platform (UWP) e .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | O serviço de voz suporta Windows e Linux. |
| SDK Java | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala suporta dispositivos Android e Speech. |
| Reconhecimento contínuo da fala | 10 minutos | Ilimitado (com SDK) | Tanto os protocolos do serviço de discurso e de discurso de Bing Suportam até 10 minutos por chamada. No entanto, o SDK do discurso reconecta-se automaticamente no tempo de paragem ou desconexão. |
| Resultados parciais ou provisórios | :heavy_check_mark: | :heavy_check_mark: | Com protocolo WebSockets ou SDK. |
| Modelos de discurso personalizados | :heavy_check_mark: | :heavy_check_mark: | Bing Speech requer uma subscrição separada do Discurso Personalizado. |
| Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | Bing Speech requer uma subscrição de Voz Personalizada separada. |
| Vozes de 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Reconhecimento de intenções de fala | Requer chamada separada da API LUIS | Integrado (com SDK) | Você pode usar uma chave LUIS com o serviço de Fala. |
| Reconhecimento de intenções simples | :heavy_minus_sign: | :heavy_check_mark: |
| Transcrição de lote de longos ficheiros áudio | :heavy_minus_sign: | :heavy_check_mark: |
| Modo de reconhecimento | Manual via endpoint URI | Automático | O modo de reconhecimento não está disponível no serviço Da fala. |
| Localidade de Endpoint | Global | Regional | Os pontos finais regionais melhoram a latência. |
| APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs do serviço de fala REST são compatíveis com bing Speech (ponto final diferente). REST APIs suportam texto-a-fala e funcionalidade limitada de fala-a-texto. |
| Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala WebSockets API é compatível com Bing Speech (ponto final diferente). Migrar para o SDK da fala, se possível, para simplificar o seu código. |
| Chamadas API de serviço ao serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecido em Bing Speech através da Biblioteca de Serviços C#. |
| SDK de código aberto | :heavy_check_mark: | :heavy_minus_sign: |

O serviço Speech utiliza um modelo de preços baseado no tempo (em vez de um modelo baseado em transações). Consulte os preços do [serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicações em desenvolvimento ou produção que usam uma API bing speech, você deve atualizá-las para usar o serviço de Fala o mais rápido possível. Consulte a [documentação](index.yml) do serviço da Fala para Os DSDs disponíveis, amostras de código e tutoriais.

As [APIs](rest-apis.md) do serviço de fala REST são compatíveis com as APIs do Discurso bing. Se está a utilizar atualmente as APIs do Bing Speech REST, só precisa de alterar o ponto final do REST e mudar para uma chave de subscrição do serviço de fala.

Os protocolos webSockets do serviço de fala também são compatíveis com os utilizados pelo Bing Speech. Recomendamos que para novos desenvolvimentos, utilize o SDK de Fala em vez de WebSockets. É uma boa ideia migrar o código existente para o SDK também. No entanto, tal como acontece com as APIs rest, o código existente que utiliza o Bing Speech via WebSockets requer apenas uma alteração no ponto final e uma chave atualizada.

Se estiver a usar uma biblioteca de clientes bing Speech para uma linguagem de programação específica, migrar para o [Speech SDK](speech-sdk.md) requer alterações na sua aplicação, porque a API é diferente. O SDK do Discurso pode tornar o seu código mais simples, ao mesmo tempo que lhe dá acesso a novas funcionalidades. O Speech SDK está disponível numa grande variedade de linguagens de programação. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento multi-plataforma.

O serviço de discurso não oferece um ponto final global. Determine se a sua aplicação funciona de forma eficiente quando utiliza um único ponto final regional para todo o seu tráfego. Caso contrário, utilize a geolocalização para determinar o ponto final mais eficiente. Você precisa de uma subscrição separada do serviço Speech em cada região que você usa.

Se a sua aplicação utilizar ligações de longa duração e não puder utilizar um SDK disponível, pode utilizar uma ligação WebSockets. Gerencie o limite de tempo limite de 10 minutos reconectando-se nos momentos apropriados.

Para começar com o SDK do discurso:

1. Descarregue o [Speech SDK](speech-sdk.md).
1. Trabalhe através do serviço de discurso [guias](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) e [tutoriais.](how-to-recognize-intents-from-speech-csharp.md) Consulte também as [amostras](samples.md) de código para obter experiência com as novas APIs.
1. Atualize a sua aplicação para utilizar o serviço DeFala.

## <a name="support"></a>Suporte

Os clientes do Bing Speech devem contactar o apoio ao cliente abrindo um bilhete de [apoio.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Também pode contactar-nos se o seu suporte necessitar de um plano de [apoio técnico.](https://azure.microsoft.com/support/plans/)

Para o serviço de Discurso, SDK e apoio da API, visite a página de [apoio](support.md)ao serviço da Fala .

## <a name="next-steps"></a>Passos seguintes

* [Experimente o serviço de fala gratuitamente](get-started.md)
* [Quickstart: Reconheça o discurso numa aplicação da UWP usando o Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Consulte também
* [Notas de lançamento do serviço de fala](releasenotes.md)
* [O que é o serviço da Fala](overview.md)
* [Discurso e documentação do SDK da fala](speech-sdk.md#get-the-speech-sdk)
