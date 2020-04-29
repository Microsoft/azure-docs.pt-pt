---
title: Migrar da API do Discurso tradutor para o serviço de discurso
titleSuffix: Azure Cognitive Services
description: Aprenda a migrar as suas candidaturas da API do Discurso tradutor para o serviço de Discurso.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77560903"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar da API do Discurso tradutor para o serviço de discurso

Utilize este artigo para migrar as suas aplicações da API do Discurso do Tradutor da Microsoft para o [serviço de Fala](index.yml). Este guia descreve as diferenças entre o serviço de Discurso de Tradutor e Discurso, e sugere estratégias para migrar as suas aplicações.

> [!NOTE]
> A chave de subscrição da API do Discurso do Tradutor não será aceite pelo serviço de Discurso. Terá de criar uma nova subscrição do serviço Speech.

## <a name="comparison-of-features"></a>Comparação de características

| Funcionalidade                                           | API de Voz do Microsoft Translator                                  | Serviço de voz | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para a fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto final global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço de discurso não oferece um ponto final global. Um ponto final global pode automaticamente direcionar o tráfego para o ponto final regional mais próximo, diminuindo a latência na sua aplicação.                                                    |
| Pontos finais regionais                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite de tempo de ligação                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma ligação WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave auth no cabeceamento                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Múltiplas línguas traduzidas num único pedido | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação](index.yml) do serviço de Fala para Os DSDs disponíveis.                                                                                                                                                    |
| Ligações WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de línguas                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço da Fala suporta a mesma gama de idiomas descritas no artigo de referência de [línguas API tradutoras.](../translator-speech/languages-reference.md) |
| Filtro de Profanação e Marcador                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de ficheiros como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informação de tempo                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de discurso personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | O serviço Speech oferece modelos de discurso personalizados que lhe permitem personalizar o reconhecimento da fala ao vocabulário único da sua organização.                                                                                                                                           |
| Modelos de tradução personalizada                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Subscrever a Microsoft Text Translation API permite-lhe utilizar o [Tradutor Personalizado](https://www.microsoft.com/translator/business/customization/) para utilizar os seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicações em desenvolvimento ou produção que usam a API de Discurso tradutor, deve atualizá-las para usar o serviço de Fala. Consulte a documentação do [serviço da Fala](index.yml) para Os DSDs disponíveis, amostras de código e tutoriais. Considere o seguinte quando estiver a migrar:

* O serviço de discurso não oferece um ponto final global. Determine se a sua aplicação funciona de forma eficiente quando utiliza um único ponto final regional para todo o seu tráfego. Caso contrário, utilize a geolocalização para determinar o ponto final mais eficiente.

* Se a sua aplicação utilizar ligações de longa duração e não puder utilizar os SDKs disponíveis, pode utilizar uma ligação WebSockets. Gerencie o limite de tempo limite de 10 minutos reconectando-se nos momentos apropriados.

* Se a sua aplicação utilizar a API de Texto tradutor e a API de Discurso tradutor para ativar modelos de tradução personalizadas, pode adicionar IDs de categoria diretamente utilizando o serviço de Fala.

* Ao contrário da API do Discurso tradutor, o serviço de Fala pode completar traduções em várias línguas num único pedido.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o serviço de fala gratuitamente](get-started.md)
* [Quickstart: Reconheça o discurso numa aplicação da UWP usando o Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Consulte também

* [O que é o serviço da Fala](overview.md)
* [Discurso e documentação do SDK da fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
