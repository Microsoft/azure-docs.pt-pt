---
title: Migrar a partir da API de voz do Translator para o serviço de voz
titleSuffix: Azure Cognitive Services
description: Saiba como migrar as suas aplicações de API de voz do Translator para os serviços de voz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: 35f970e81d27511bd35610bc2988a5ea4832906b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898640"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar a partir da API de voz do Translator para o serviço de voz

Utilize este artigo para migrar as suas aplicações de API de voz do Microsoft Translator para o [serviço de voz](index.yml). Este guia descreve as diferenças entre a API de voz do Translator e o serviço de voz e sugere estratégias para migrar seus aplicativos.

> [!NOTE]
> A chave de subscrição de API de voz do Translator não aceite pelo serviço de voz. Terá de criar uma nova subscrição de serviços de voz.

## <a name="comparison-of-features"></a>Comparação de funcionalidades

| Funcionalidade                                           | API de Voz do Microsoft Translator                                  | Serviços de Voz | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução de texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução de voz                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto final global                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Os serviços de voz não oferecem um ponto final global. Um ponto final global automaticamente pode direcionar o tráfego para o ponto de extremidade regional mais próximo, reduzindo a latência em seu aplicativo.                                                    |
| Pontos finais regionais                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite de tempo de ligação                             | 90 minutos                                               | É ilimitado com o SDK. 10 minutos com uma ligação de WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Vários idiomas traduzido num único pedido | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação de serviços de voz](index.yml) para SDKs disponíveis.                                                                                                                                                    |
| Ligações de WebSockets                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de idiomas                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Os serviços de voz suporta o mesmo intervalo de idiomas descritas a [referência de idiomas da API do Translator](../translator-speech/languages-reference.md) artigo. |
| Filtro de palavras ofensivas e marcador                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de temporização                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de voz personalizada                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Os serviços de voz oferecem modelos de voz personalizada que lhe permitem personalizar o reconhecimento de fala para vocabulário exclusivo da sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Subscrever a API de tradução de texto do Microsoft permite-lhe utilizar [Translator personalizado](https://www.microsoft.com/translator/business/customization/) para utilizar os seus dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se ou sua organização tiver aplicações em desenvolvimento ou de produção que utilizam a API de voz do Translator, atualize-os para utilizar o serviço de voz. Consulte a [serviço de voz](index.yml) documentação para disponíveis SDKs, exemplos de códigos e tutoriais. Quando estiver a migrar, considere o seguinte:

* Os serviços de voz não oferecem um ponto final global. Determine se a aplicação funciona com eficiência quando utiliza um único ponto de final de regional para todo o tráfego. Caso contrário, utilize a localização geográfica para determinar o ponto de extremidade mais eficiente.

* Se a sua aplicação utiliza ligações longa duração e não é possível utilizar os SDK disponíveis, pode utilizar uma ligação de WebSockets. Gerir o limite de tempo limite de 10 minutos a restabelecer ligação nos momentos apropriados.

* Se a sua aplicação utilizar a API de texto do Translator e a API de voz do Translator para ativar modelos de tradução personalizadas, pode adicionar os IDs de categoria diretamente através do serviço de voz.

* Ao contrário da API de voz do Translator, os serviços de voz podem concluir as traduções em vários idiomas numa única solicitação.

## <a name="next-steps"></a>Passos Seguintes

* [Experimente gratuitamente os serviços de voz](get-started.md)
* [Quickstart: Reconhecer voz numa aplicação UWP utilizando o SDK de voz](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também

* [O que é o serviço de voz](overview.md)
* [Documentação de serviços de voz e o SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
