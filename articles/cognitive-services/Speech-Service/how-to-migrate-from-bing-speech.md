---
title: Migrar a partir de voz do Bing nos serviços de voz do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma subscrição existente de voz do Bing para os serviços de voz do Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848971"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar de voz do Bing para o serviço de voz

Utilize este artigo para migrar as suas aplicações da API de voz do Bing para o serviço de voz.

Este artigo descreve as diferenças entre as APIs de voz do Bing e os serviços de voz e sugere estratégias para migrar seus aplicativos. A chave de subscrição de API de voz do Bing não funcionará com o serviço de voz; precisará de uma nova subscrição de serviços de voz.

Uma única chave de subscrição de serviços de voz concede acesso para as seguintes funcionalidades. Cada uma é medida em separado, pelo que apenas lhe são cobradas as funcionalidades que utilizar.

* [Conversão de voz em texto](speech-to-text.md)
* [Conversão de voz em texto personalizada](https://cris.ai)
* [Conversão de texto em voz](text-to-speech.md)
* [Vozes personalizadas para conversão de texto em voz](how-to-customize-voice-font.md)
* [Tradução de Voz](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de voz](speech-sdk.md) é uma substituição funcional para as bibliotecas de cliente de voz do Bing, mas utiliza uma API diferente.

## <a name="comparison-of-features"></a>Comparação de funcionalidades

Os serviços de fala são muito semelhantes de voz do Bing, com as seguintes diferenças.

Funcionalidade | Voz do Bing | Serviços de Voz | Detalhes
-|-|-|-
C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Serviços de voz suporta Windows e Linux.
SDK Java | :heavy_check_mark: | :heavy_check_mark: | Os serviços de voz suportam o Android e dispositivos de voz.
SDK C# | :heavy_check_mark: | :heavy_check_mark: | Serviços de voz oferece suporte a Windows 10, a plataforma Universal do Windows (UWP) e o .NET Standard 2.0.
Reconhecimento de fala contínua | 10 minutos | Ilimitado (com o SDK) | Voz do Bing e protocolos de WebSockets de serviços de voz suportam até 10 minutos por chamada. No entanto, o SDK de voz automaticamente volta no tempo limite de ligar ou desligar.
Resultados parciais ou intermediárias | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo de WebSockets ou o SDK.
Modelos de voz personalizada | :heavy_check_mark: | :heavy_check_mark: | Voz do Bing requer uma subscrição separada de voz personalizada.
Tipos de voz personalizada | :heavy_check_mark: | :heavy_check_mark: | Voz do Bing requer uma subscrição separada de voz personalizada.
Vozes 24 kHz | : heavy_minus_sign: | :heavy_check_mark:
Reconhecimento da intenção do discurso | Requer separada chamada à API de LUIS | Integrada (com o SDK) |  Pode utilizar uma chave de LUIS com o serviço de voz.
Reconhecimento da intenção Simple | : heavy_minus_sign: | :heavy_check_mark:
Transcrição de batch de arquivos de áudio há muito tempo | : heavy_minus_sign: | :heavy_check_mark:
Modo de reconhecimento | Manual através do URI do ponto final | Automático | Modo de reconhecimento não está disponível no serviço de voz.
Localidade do ponto final | Global | Regional | Pontos finais regionais melhoram a latência.
APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs de REST de serviços de voz são compatíveis com a voz do Bing (ponto de extremidade diferente). REST APIs suportam a funcionalidade de voz em texto de texto para voz e limitada.
Protocolos de WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API de WebSockets de serviços de voz é compatível com a voz do Bing (ponto de extremidade diferente). Migre para o SDK de voz se possível, para simplificar o seu código.
Chamadas à API de serviços | :heavy_check_mark: | : heavy_minus_sign: | Fornecido em voz do Bing através da biblioteca de serviço c#.
SDK de código aberto | :heavy_check_mark: | : heavy_minus_sign: |

Os serviços de voz utilizam um modelo de preços baseados no tempo (em vez de um modelo baseado em transações). Ver [preços de serviços de voz](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se ou sua organização tiver aplicativos no desenvolvimento ou de produção que utilizam uma API de voz do Bing, atualize-os para utilizar os serviços de voz logo que possível. Consulte a [documentação de serviços de voz](index.yml) para disponíveis SDKs, exemplos de códigos e tutoriais.

Os serviços de voz [REST APIs](rest-apis.md) são compatíveis com as APIs de voz do Bing. Se estiver a utilizar atualmente as APIs de REST de voz do Bing, precisará apenas alterar o ponto final REST e mudar para uma chave de subscrição de serviços de voz.

Os protocolos de WebSockets de serviços de voz também são compatíveis com os usados por voz do Bing. Recomendamos que, para desenvolvimento de novas, utilize o SDK de voz em vez de WebSockets. É uma boa idéia para migrar o código existente para o SDK também. No entanto, como com as APIs REST, código existente que utilize a voz do Bing através de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se estiver a utilizar uma biblioteca de cliente de voz do Bing para uma linguagem de programação específica, migrar para o [SDK de voz](speech-sdk.md) requer alterações ao seu aplicativo, porque a API é diferente. O SDK de voz pode tornar o código mais simples, ao mesmo tempo dando acesso aos novos recursos.

Atualmente, o SDK de voz suporta c# (Windows 10, UWP, .NET Standard), o Java (dispositivos Android e personalizados), Objective C (iOS), C++ (Windows e Linux) e JavaScript. APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento de Multiplataforma.

Os serviços de voz não oferecem um ponto final global. Determine se a aplicação funciona com eficiência quando utiliza um único ponto de final de regional para todo o tráfego. Caso contrário, utilize a localização geográfica para determinar o ponto de extremidade mais eficiente. Precisa de uma subscrição de serviços de voz separada em cada região a que utilizar.

Se a sua aplicação utiliza ligações longa duração e não é possível utilizar um SDK disponível, pode utilizar uma ligação de WebSockets. Gerir o limite de tempo limite de 10 minutos a restabelecer ligação nos momentos apropriados.

Para começar a utilizar com o SDK de voz:

1. Transfira o [SDK de voz](speech-sdk.md).
1. Trabalho através dos serviços de voz [guias de início rápido](quickstart-csharp-dotnet-windows.md) e [tutoriais](how-to-recognize-intents-from-speech-csharp.md). Observe também o [exemplos de código](samples.md) obter experiência com as novas APIs.
1. Atualize a sua aplicação para utilizar os serviços de voz.

## <a name="support"></a>Suporte

Os clientes de voz do Bing devem contactar o suporte ao cliente ao abrir um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Também pode contactar-nos se precisar de sua necessidade de suporte um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Para obter suporte de API, SDK e serviço de voz, visite os serviços de voz [página de suporte](support.md).

## <a name="next-steps"></a>Passos Seguintes

* [Experimente gratuitamente os serviços de voz](get-started.md)
* [Quickstart: Reconhecer voz numa aplicação UWP utilizando o SDK de voz](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também
* [Notas de versão de serviços de voz](releasenotes.md)
* [O que é o serviço de voz](overview.md)
* [Documentação de serviços de voz e o SDK de voz](speech-sdk.md#get-the-sdk)
