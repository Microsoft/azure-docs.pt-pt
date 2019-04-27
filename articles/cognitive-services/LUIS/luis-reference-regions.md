---
title: Regiões de publicação e pontos finais
titleSuffix: Azure Cognitive Services
description: 3 regiões de criação e seus portais suportam todas as regiões de publicação muitos. A região em que publique a sua aplicação LUIS corresponde à região ou localização que especificar no portal do Azure ao criar uma chave de ponto final do Azure LUIS. Quando publica uma aplicação, o LUIS gera automaticamente um URL de ponto final para a região associada à chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599272"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Criação e publicação de regiões e as chaves associadas

Três regiões de criação são suportados pelo portais de LUIS correspondentes. Para publicar uma aplicação do LUIS mais do que uma região, tem pelo menos uma chave por região. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de criação de LUIS
Existem três portais de criação da LUIS, com base na região. Tem de criar e publicar na mesma região. 

|LUIS|Região de criação|Nome da região do Azure|
|--|--|--|
|[www.Luis.ai][www.luis.ai]|As<br>não Europa<br>não Austrália| `westus`|
|[au.Luis.ai][au.luis.ai]|Austrália| `australiaeast`|
|[eu.Luis.ai][eu.luis.ai]|Europa|`westeurope`|

Criação de regiões têm [ativação pós-falha regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões de publicação e de recursos do Azure
A aplicação for publicada para todas as regiões associadas com os recursos de LUIS, adicionados no portal do LUIS. Por exemplo, para uma aplicação criada no [www.luis.ai][www.luis.ai], se criar um recurso de LUIS ou serviço cognitivo no **westus** e [adicioná-lo para a aplicação como um recurso ](luis-how-to-azure-subscription.md), a aplicação for publicada nessa região. 

## <a name="public-apps"></a>Aplicações públicas
Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região é associada à sua chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicação de regiões estão associadas a criação de regiões

Só pode ser publicada a aplicação de região de criação para odpovídající publicar região. Se a aplicação está atualmente na região errada de criação, exportar a aplicação e importe-o para a região de criação correta para a sua região de publicação. 

Aplicações de LUIS criadas em https://www.luis.ai pode ser publicado para todos os pontos finais, exceto o [Europeia](#publishing-to-europe) e [australiana](#publishing-to-australia) regiões. 

## <a name="publishing-to-europe"></a>Publicação para a Europa

Para publicar as regiões da Europa, crie LUIS aplicações à https://eu.luis.ai apenas. Se tentar publicar em qualquer lugar que outra com uma chave na região da Europa, LUIS exibe uma mensagem de aviso. Em alternativa, utilize https://eu.luis.ai. Aplicações de LUIS criadas às [ https://eu.luis.ai ] [ eu.luis.ai] não migram para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="europe-publishing-regions"></a>Europa ocidental de publicação

 Região global | Região de API de criação e criação de Web site| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.Luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.Luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.Luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.Luis.ai][eu.luis.ai]| Reino Unido Sul<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicação da Austrália

Para publicar as regiões australianas, crie LUIS aplicações em https://au.luis.ai apenas. Se tentar publicar em qualquer lugar que outra com uma chave na região australiano, LUIS exibe uma mensagem de aviso. Em alternativa, utilize https://au.luis.ai. Aplicações de LUIS criadas às [ https://au.luis.ai ] [ au.luis.ai] não migram para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="australia-publishing-regions"></a>Regiões de publicação da Austrália

 Região global | Região de API de criação e criação de Web site| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.Luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicação de outras regiões

Para publicar as outras regiões, crie LUIS aplicações à [ https://www.luis.ai ](https://www.luis.ai) apenas. 

## <a name="other-publishing-regions"></a>Outras regiões de publicação

 Região global | Região de API de criação e criação de Web site| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Ásia Oriental<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.Luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.Luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.Luis.ai][www.luis.ai] | EUA Central<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.Luis.ai][www.luis.ai] | EUA Leste<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.Luis.ai][www.luis.ai] | EUA Leste 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.Luis.ai][www.luis.ai] | EUA Centro-Norte<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | `westus`<br>[www.Luis.ai][www.luis.ai] | EUA Centro-Sul<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte |`westus`<br>[www.Luis.ai][www.luis.ai] | EUA Centro-Oeste<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.Luis.ai][www.luis.ai] | EUA Oeste<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte |`westus`<br>[www.Luis.ai][www.luis.ai] | EUA Oeste 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | `westus`<br>[www.Luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Pontos Finais

Atualmente, o LUIS tem 2 pontos de extremidade: um para a criação e outro para análise de previsão de consulta.

|Objetivo|do IdP|
|--|--|
|Criação de conteúdos|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (predição de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela seguinte explica os parâmetros, marcados com chavetas `{}`, na tabela anterior.

|Parâmetro|Objetivo|
|--|--|
|Região|Região do Azure - criação e publicação têm diferentes regiões|
|appID|ID da aplicação LUIS utilizado na rota de URL e encontrado no dashboard de aplicações|
|p|texto de expressão enviado a partir do aplicativo de cliente, como o bot de bate-papo|

## <a name="failover-regions"></a>Regiões de ativação pós-falha

Cada região tem uma região secundária para efetuar a ativação pós-falha. Falha na Europa, a ativação pós-falha dentro da Europa e na Austrália efetua a ativação pós-falha dentro da Austrália.

Criação de regiões têm [ativação pós-falha regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-concebidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
