---
title: Migrar da API do Discurso do Tradutor para o serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Saiba como migrar as suas aplicações da API do Discurso do Tradutor para o serviço de Fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95013643"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar da API do Discurso do Tradutor para o serviço de Discurso

Utilize este artigo para migrar as suas aplicações da API do Discurso do Tradutor da Microsoft para o [serviço Speech](index.yml). Este guia descreve as diferenças entre o serviço de API de Discurso de Tradutor e Discurso, e sugere estratégias para migrar as suas aplicações.

> [!NOTE]
> A sua chave de subscrição de API de Discurso de Tradutor não será aceite pelo serviço Speech. Terá de criar uma nova subscrição do serviço Speech.

## <a name="comparison-of-features"></a>Comparação de características

| Funcionalidade                                           | API de Voz do Microsoft Translator                                  | Serviço de voz | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para a fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto final global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço de discurso não oferece um ponto final global. Um ponto final global pode dirigir automaticamente o tráfego para o ponto final regional mais próximo, diminuindo a latência na sua aplicação.                                                    |
| Pontos finais regionais                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite de tempo de ligação                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma ligação WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave auth no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Várias línguas traduzidas num único pedido | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação](index.yml) do serviço de discurso para os SDKs disponíveis.                                                                                                                                                    |
| Ligações WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Idiomas API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço Discurso suporta a mesma gama de línguas descritas no artigo de [referência de línguas tradutoras.]() |
| Filtro e marcador de profanação                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de ficheiros como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informação de cronometragem                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | O serviço Speech oferece modelos de fala personalizados que lhe permitem personalizar o reconhecimento de voz ao vocabulário único da sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A subscrição da API de Tradução de Texto da Microsoft permite-lhe utilizar o [Tradutor Personalizado](https://www.microsoft.com/translator/business/customization/) para utilizar os seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou a sua organização tiverem aplicações em desenvolvimento ou produção que utilizem a API de Discurso do Tradutor, deve atualizá-las para usar o serviço Speech. Consulte a documentação do [serviço de discurso](index.yml) para SDKs disponíveis, amostras de código e tutoriais. Considere o seguinte quando estiver a migrar:

* O serviço de discurso não oferece um ponto final global. Determine se a sua aplicação funciona de forma eficiente quando utilizar um único ponto de terminamento regional para todo o seu tráfego. Caso contrário, utilize a geolocalização para determinar o ponto final mais eficiente.

* Se a sua aplicação utilizar ligações de longa duração e não puder utilizar os SDKs disponíveis, pode utilizar uma ligação WebSockets. Gerencie o prazo de 10 minutos reconectando-se nos horários adequados.

* Se a sua aplicação utilizar o serviço De Tradutor e a API de Discurso de Tradutor para permitir modelos de tradução personalizados, pode adicionar IDs de categoria diretamente utilizando o serviço Speech.

* Ao contrário da API de Discurso de Tradutor, o serviço de Fala pode completar traduções em várias línguas num único pedido.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o serviço de fala gratuitamente](overview.md#try-the-speech-service-for-free)
* [Quickstart: Reconhecer o discurso numa aplicação UWP utilizando o Speech SDK](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Ver também

* [O que é o serviço de fala](overview.md)
* [Serviço de fala e documentação SDK de fala](./speech-devices-sdk-quickstart.md?pivots=platform-android)