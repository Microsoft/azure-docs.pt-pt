---
title: Migrar do Fala do Bing para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma assinatura existente do Fala do Bing para o serviço de fala dos serviços cognitivas do Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: c0e1cc2fc1b3d4aed82c5442d2d3e23a1272fab5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805948"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar do Fala do Bing para o serviço de fala

Use este artigo para migrar seus aplicativos do API de Fala do Bing para o serviço de fala.

Este artigo descreve as diferenças entre as APIs de Fala do Bing e o serviço de fala e sugere estratégias para migrar seus aplicativos. Sua chave de assinatura do API de Fala do Bing não funcionará com o serviço de fala; Você precisará de uma nova assinatura do serviço de fala.

Uma única chave de assinatura do serviço de fala concede acesso aos recursos a seguir. Cada uma é medida em separado, pelo que apenas lhe são cobradas as funcionalidades que utilizar.

* [Conversão de voz em texto](speech-to-text.md)
* [Conversão de voz em texto personalizada](https://cris.ai)
* [Conversão de texto em voz](text-to-speech.md)
* [Vozes personalizadas para conversão de texto em voz](how-to-customize-voice-font.md)
* [Tradução de Voz](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de fala](speech-sdk.md) é uma substituição funcional para as bibliotecas de cliente fala do Bing, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de recursos

O serviço de fala é muito semelhante ao Fala do Bing, com as seguintes diferenças.

Funcionalidade | Voz do Bing | Serviço de fala | Detalhes
-|-|-|-
C++SDK | : heavy_minus_sign: | :heavy_check_mark: | O serviço de fala dá suporte ao Windows e ao Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala dá suporte a dispositivos Android e de fala.
SDK C# | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala dá suporte ao Windows 10, Plataforma Universal do Windows (UWP) e .NET Standard 2,0.
Reconhecimento de fala contínuo | 10 minutos | Ilimitado (com SDK) | Os protocolos de WebSockets do serviço de fala Fala do Bing e do Speech dão suporte a até 10 minutos por chamada. No entanto, o SDK de fala se reconecta automaticamente no tempo limite ou se desconecta.
Resultados parciais ou provisórios | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo WebSockets ou o SDK.
Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | Fala do Bing requer uma assinatura de Fala Personalizada separada.
Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | Fala do Bing requer uma assinatura de voz personalizada separada.
vozes de 24 KHz | : heavy_minus_sign: | :heavy_check_mark:
Reconhecimento da intenção do discurso | Requer chamada à API LUIS separada | Integrado (com SDK) |  Você pode usar uma chave LUIS com o serviço de fala.
Reconhecimento de intenção simples | : heavy_minus_sign: | :heavy_check_mark:
Transcrição de lote de arquivos de áudio longos | : heavy_minus_sign: | :heavy_check_mark:
Modo de reconhecimento | Manual via URI de ponto de extremidade | Automático | O modo de reconhecimento não está disponível no serviço de fala.
Localidade do ponto de extremidade | Global | Regional | Os pontos de extremidade regionais melhoram a latência.
APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs REST do serviço de fala são compatíveis com Fala do Bing (ponto de extremidade diferente). As APIs REST dão suporte à funcionalidade de conversão de texto em fala e limitada.
Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API WebSockets do serviço de fala é compatível com Fala do Bing (ponto de extremidade diferente). Migre para o SDK de fala, se possível, para simplificar seu código.
Chamadas à API de serviço a serviço | :heavy_check_mark: | : heavy_minus_sign: | Fornecido em Fala do Bing por meio C# da biblioteca de serviços.
SDK de código aberto | :heavy_check_mark: | : heavy_minus_sign: |

O serviço de fala usa um modelo de preços baseado em tempo (em vez de um modelo baseado em transações). Consulte [preços do serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usam um API de Fala do Bing, você deverá atualizá-los para usar o serviço de fala assim que possível. Consulte a [documentação do serviço de fala](index.md) para ver SDKs disponíveis, exemplos de código e tutoriais.

As [APIs REST](rest-apis.md) do serviço de fala são compatíveis com as apis de fala do Bing. Se você estiver usando o Fala do Bing APIs REST, precisará apenas alterar o ponto de extremidade REST e alternar para uma chave de assinatura do serviço de fala.

Os protocolos do WebSocket do serviço de fala também são compatíveis com aqueles usados pelo Fala do Bing. Recomendamos que, para o novo desenvolvimento, você use o SDK de fala em vez de WebSockets. Também é uma boa ideia migrar o código existente para o SDK. No entanto, assim como acontece com as APIs REST, o código existente que usa Fala do Bing por meio de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se você estiver usando uma biblioteca de cliente Fala do Bing para uma linguagem de programação específica, migrar para o [SDK de fala](speech-sdk.md) exigirá alterações no seu aplicativo, pois a API é diferente. O SDK de fala pode tornar seu código mais simples, enquanto também oferece acesso a novos recursos.

Atualmente, o SDK de fala C# dá suporte ([detalhes aqui](https://aka.ms/csspeech)), Java (dispositivos Android e personalizados), Objective C ( C++ Ios), (Windows e Linux) e JavaScript. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento de várias plataformas.

O serviço de fala não oferece um ponto de extremidade global. Determine se seu aplicativo funciona com eficiência quando usa um único ponto de extremidade regional para todo o seu tráfego. Caso contrário, use geolocalização para determinar o ponto de extremidade mais eficiente. Você precisa de uma assinatura de serviço de fala separada em cada região usada.

Se seu aplicativo usar conexões de vida útil longa e não puder usar um SDK disponível, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos reconectando nos horários apropriados.

Para começar a usar o SDK de fala:

1. Transfira o [SDK de voz](speech-sdk.md).
1. Trabalhe nos [guias de início rápido](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) e [tutoriais](how-to-recognize-intents-from-speech-csharp.md)do serviço de fala. Examine também os [exemplos de código](samples.md) para obter experiência com as novas APIs.
1. Atualize seu aplicativo para usar o serviço de fala.

## <a name="support"></a>Suporte

Fala do Bing clientes devem contatar o atendimento ao cliente abrindo um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Você também pode entrar em contato conosco se sua necessidade de suporte exigir um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Para o suporte ao serviço de fala, ao SDK e à API, visite a [página de suporte](support.md)do serviço de fala.

## <a name="next-steps"></a>Passos seguintes

* [Experimente gratuitamente o serviço de fala](get-started.md)
* [Início rápido: reconhecer a fala em um aplicativo UWP usando o SDK de fala](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Ver também
* [Notas de versão do serviço de fala](releasenotes.md)
* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](speech-sdk.md#get-the-sdk)
