---
title: Migrar do API de Tradução de Fala para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar seus aplicativos do API de Tradução de Fala para o serviço de fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559560"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar do API de Tradução de Fala para o serviço de fala

Use este artigo para migrar seus aplicativos do Microsoft API de Tradução de Fala para o [serviço de fala](index.yml). Este guia descreve as diferenças entre o API de Tradução de Fala e o serviço de fala e sugere estratégias para migrar seus aplicativos.

> [!NOTE]
> Sua chave de assinatura do API de Tradução de Fala não será aceita pelo serviço de fala. Você precisará criar uma nova assinatura dos serviços de fala.

## <a name="comparison-of-features"></a>Comparação de recursos

| Funcionalidade                                           | API de Voz do Microsoft Translator                                  | Serviços de Voz | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto de extremidade global                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Os serviços de fala não oferecem um ponto de extremidade global. Um ponto de extremidade global pode direcionar automaticamente o tráfego para o ponto de extremidade regional mais próximo, diminuindo a latência em seu aplicativo.                                                    |
| Pontos de extremidade regionais                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite de tempo de conexão                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma conexão WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Vários idiomas traduzidos em uma única solicitação | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação dos serviços de fala](index.yml) para SDKs disponíveis.                                                                                                                                                    |
| Conexões WebSockets                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de idiomas                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Os serviços de fala oferecem suporte ao mesmo intervalo de idiomas descritos no artigo de [referência de idiomas da API do tradutor](../translator-speech/languages-reference.md) . |
| Filtro e marcador de profanação                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de tempo                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Os serviços de fala oferecem modelos de fala personalizados que permitem que você personalize o reconhecimento de fala para o vocabulário exclusivo de sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Assinar a API de tradução de texto da Microsoft permite que você use o [Tradutor personalizado](https://www.microsoft.com/translator/business/customization/) para usar seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usam o API de Tradução de Fala, você deverá atualizá-los para usar o serviço de fala. Consulte a documentação do [serviço de fala](index.yml) para ver SDKs disponíveis, exemplos de código e tutoriais. Considere o seguinte ao migrar:

* Os serviços de fala não oferecem um ponto de extremidade global. Determine se seu aplicativo funciona com eficiência quando usa um único ponto de extremidade regional para todo o seu tráfego. Caso contrário, use geolocalização para determinar o ponto de extremidade mais eficiente.

* Se seu aplicativo usar conexões de vida útil longa e não puder usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos reconectando nos horários apropriados.

* Se seu aplicativo usar o API de Tradução de Texto e API de Tradução de Fala para habilitar modelos de tradução personalizados, você poderá adicionar IDs de categoria diretamente usando o serviço de fala.

* Ao contrário do API de Tradução de Fala, os serviços de fala podem concluir traduções em vários idiomas em uma única solicitação.

## <a name="next-steps"></a>Passos seguintes

* [Experimente os serviços de fala gratuitamente](get-started.md)
* [Quickstart: Reconhecer a fala em um aplicativo UWP usando o SDK de fala](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também

* [O que é o serviço de voz](overview.md)
* [Documentação dos serviços de fala e do SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
