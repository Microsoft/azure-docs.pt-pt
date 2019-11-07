---
title: Regiões de publicação & pontos de extremidade-LUIS
titleSuffix: Azure Cognitive Services
description: A região especificada no portal do Azure é a mesma em que você publicará o aplicativo LUIS e uma URL de ponto de extremidade será gerada para essa mesma região.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669191"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de criação e publicação e as chaves associadas

Três regiões de criação têm suporte dos portais correspondentes do LUIS. Para publicar um aplicativo LUIS em mais de uma região, você precisa de pelo menos uma chave por região. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de criação do LUIS
Há três portais de criação de LUIS, com base na região. Você deve criar e publicar na mesma região. 

|LUIS|Região de criação|Nome da região do Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|As<br>Não Europa<br>Não Austrália| `westus`|
|[au.luis.ai][au.luis.ai]|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Regiões de criação têm [regiões de failover emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões de publicação e recursos do Azure
O aplicativo é publicado em todas as regiões associadas aos recursos de LUIS adicionados no portal do LUIS. Por exemplo, para um aplicativo criado em [www.Luis.ai][www.luis.ai], se você criar um recurso de serviço Luis ou cognitiva na **westus** e [adicioná-lo ao aplicativo como um recurso](luis-how-to-azure-subscription.md), o aplicativo será publicado nessa região. 

## <a name="public-apps"></a>Aplicativos públicos
Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso LUIS baseada em região possa acessar o aplicativo em qualquer região associada à sua chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões de publicação estão vinculadas a regiões de criação

O aplicativo de região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver atualmente na região de criação errada, exporte o aplicativo e importe-o para a região de criação correta para sua região de publicação. 

Os aplicativos LUIS criados em https://www.luis.ai podem ser publicados em todos os pontos de extremidade, exceto nas regiões [Europeia](#publishing-to-europe) e [Austrália](#publishing-to-australia) . 

## <a name="publishing-to-europe"></a>Publicando na Europa

Para publicar nas regiões européias, você cria aplicativos LUIS somente em https://eu.luis.ai. Se você tentar publicar em qualquer outro lugar usando uma chave na região da Europa, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. Os aplicativos LUIS criados em [https://eu.luis.ai][eu.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="europe-publishing-regions"></a>Regiões de publicação da Europa

 Região global | Criando a região da API & site de criação| Publicando & a região de consulta<br>`API region name`   |  Formato de URL do ponto de extremidade   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Reino Unido Sul<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicando na Austrália

Para publicar nas regiões australianas, você cria aplicativos LUIS somente em https://au.luis.ai. Se você tentar publicar em qualquer outro lugar usando uma chave na região australiana, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://au.luis.ai. Os aplicativos LUIS criados em [https://au.luis.ai][au.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="australia-publishing-regions"></a>Regiões de publicação da Austrália

 Região global | Criando a região da API & site de criação| Publicando & a região de consulta<br>`API region name`   |  Formato de URL do ponto de extremidade   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicando em outras regiões

Para publicar em outras regiões, você cria aplicativos LUIS somente em [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Outras regiões de publicação

 Região global | Criando a região da API & site de criação| Publicando & a região de consulta<br>`API region name`   |  Formato de URL do ponto de extremidade   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| Norte da África do Sul<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Oriental<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | EUA Central<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | EUA Leste<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | EUA Leste 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | EUA Centro-Norte<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | EUA Centro-Sul<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | EUA Centro-Oeste<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | EUA Oeste<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | EUA Oeste 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Pontos Finais

O LUIS atualmente tem dois pontos de extremidade: um para a criação e outro para análise de previsão de consulta.

|Objetivo|do IdP|
|--|--|
|Criação|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (previsão de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela a seguir explica os parâmetros, denotados com chaves `{}`, na tabela anterior.

|Parâmetro|Objetivo|
|--|--|
|.|Região do Azure – criação e publicação têm regiões diferentes|
|appID|ID do aplicativo LUIS usada na rota de URL e encontrada no painel do aplicativo|
|perguntas|texto expressão enviado do aplicativo cliente, como bot de chat|

## <a name="failover-regions"></a>Regiões de failover

Cada região tem uma região secundária para fazer failover para. O failover da Europa na Europa e na Austrália faz failover dentro da Austrália.

Regiões de criação têm [regiões de failover emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
