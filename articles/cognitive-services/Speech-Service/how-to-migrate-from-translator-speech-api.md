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
ms.openlocfilehash: ba9484bd5b04e5a79da53a0bb78877153be42a43
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805914"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar do API de Tradução de Fala para o serviço de fala

Use este artigo para migrar seus aplicativos do Microsoft API de Tradução de Fala para o [serviço de fala](index.md). Este guia descreve as diferenças entre o API de Tradução de Fala e o serviço de fala e sugere estratégias para migrar seus aplicativos.

> [!NOTE]
> Sua chave de assinatura do API de Tradução de Fala não será aceita pelo serviço de fala. Você precisará criar uma nova assinatura do serviço de fala.

## <a name="comparison-of-features"></a>Comparação de recursos

| Funcionalidade                                           | API de Voz de Tradutor                                  | Serviço de fala | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto de extremidade global                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | O serviço de fala não oferece um ponto de extremidade global. Um ponto de extremidade global pode direcionar automaticamente o tráfego para o ponto de extremidade regional mais próximo, diminuindo a latência em seu aplicativo.                                                    |
| Pontos de extremidade regionais                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite de tempo de conexão                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma conexão WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Vários idiomas traduzidos em uma única solicitação | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação do serviço de fala](index.md) para SDKs disponíveis.                                                                                                                                                    |
| Conexões WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de idiomas                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | O serviço de fala dá suporte ao mesmo intervalo de idiomas descrito no artigo de [referência de idiomas da API do tradutor](../translator-speech/languages-reference.md) . |
| Filtro e marcador de profanação                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de tempo                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | O serviço de fala oferece modelos de fala personalizados que permitem que você personalize o reconhecimento de fala para o vocabulário exclusivo de sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Assinar a API de tradução de texto da Microsoft permite que você use o [Tradutor personalizado](https://www.microsoft.com/translator/business/customization/) para usar seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usam o API de Tradução de Fala, você deverá atualizá-los para usar o serviço de fala. Consulte a documentação do [serviço de fala](index.md) para ver SDKs disponíveis, exemplos de código e tutoriais. Considere o seguinte ao migrar:

* O serviço de fala não oferece um ponto de extremidade global. Determine se seu aplicativo funciona com eficiência quando usa um único ponto de extremidade regional para todo o seu tráfego. Caso contrário, use geolocalização para determinar o ponto de extremidade mais eficiente.

* Se seu aplicativo usar conexões de vida útil longa e não puder usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos reconectando nos horários apropriados.

* Se seu aplicativo usar o API de Tradução de Texto e API de Tradução de Fala para habilitar modelos de tradução personalizados, você poderá adicionar IDs de categoria diretamente usando o serviço de fala.

* Ao contrário do API de Tradução de Fala, o serviço de fala pode concluir traduções em vários idiomas em uma única solicitação.

## <a name="next-steps"></a>Passos seguintes

* [Experimente gratuitamente o serviço de fala](get-started.md)
* [Início rápido: reconhecer a fala em um aplicativo UWP usando o SDK de fala](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Ver também

* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
