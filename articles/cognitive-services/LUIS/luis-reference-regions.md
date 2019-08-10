---
title: Regiões de publicação & pontos de extremidade-LUIS
titleSuffix: Azure Cognitive Services
description: 3 regiões de criação e seus portais dão suporte a todas as várias regiões de publicação. A região em que publique a sua aplicação LUIS corresponde à região ou localização que especificar no portal do Azure ao criar uma chave de ponto final do Azure LUIS. Quando publica uma aplicação, o LUIS gera automaticamente um URL de ponto final para a região associada à chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: f859047caf64d27ef63783ecd42f12fc9b79b4fd
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933355"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de criação e publicação e as chaves associadas

Três regiões de criação têm suporte dos portais correspondentes do LUIS. Para publicar uma aplicação do LUIS mais do que uma região, tem pelo menos uma chave por região. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de criação do LUIS
Há três portais de criação de LUIS, com base na região. Tem de criar e publicar na mesma região. 

|LUIS|Região de criação|Nome da região do Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|As<br>não Europa<br>não Austrália| `westus`|
|[au.luis.ai][au.luis.ai]|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Regiões de criação têm [regiões de failover emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões de publicação e recursos do Azure
A aplicação for publicada para todas as regiões associadas com os recursos de LUIS, adicionados no portal do LUIS. Por exemplo, para um aplicativo criado em [www.Luis.ai][www.luis.ai], se você criar um recurso de serviço Luis ou cognitiva na westus e [adicioná-lo ao aplicativo como um recurso](luis-how-to-azure-subscription.md), o aplicativo será publicado nessa região. 

## <a name="public-apps"></a>Aplicações públicas
Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região é associada à sua chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões de publicação estão vinculadas a regiões de criação

Só pode ser publicada a aplicação de região de criação para odpovídající publicar região. Se a aplicação está atualmente na região errada de criação, exportar a aplicação e importe-o para a região de criação correta para a sua região de publicação. 

Aplicações de LUIS criadas em https://www.luis.ai pode ser publicado para todos os pontos finais, exceto o [Europeia](#publishing-to-europe) e [australiana](#publishing-to-australia) regiões. 

## <a name="publishing-to-europe"></a>Publicação para a Europa

Para publicar as regiões da Europa, crie LUIS aplicações à https://eu.luis.ai apenas. Se tentar publicar em qualquer lugar que outra com uma chave na região da Europa, LUIS exibe uma mensagem de aviso. Em alternativa, utilize https://eu.luis.ai. Os aplicativos Luis criados [https://eu.luis.ai][eu.luis.ai] em não são migrados automaticamente para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="europe-publishing-regions"></a>Regiões de publicação da Europa

 Região global | Criando a região da API & site de criação| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Reino Unido Sul<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicação da Austrália

Para publicar as regiões australianas, crie LUIS aplicações em https://au.luis.ai apenas. Se tentar publicar em qualquer lugar que outra com uma chave na região australiano, LUIS exibe uma mensagem de aviso. Em alternativa, utilize https://au.luis.ai. Os aplicativos Luis criados [https://au.luis.ai][au.luis.ai] em não são migrados automaticamente para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="australia-publishing-regions"></a>Regiões de publicação da Austrália

 Região global | Criando a região da API & site de criação| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicando em outras regiões

Para publicar em outras regiões, você cria aplicativos Luis somente em [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Outras regiões de publicação

 Região global | Criando a região da API & site de criação| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
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
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
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
|Criação de conteúdos|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (predição de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela seguinte explica os parâmetros, marcados com chavetas `{}`, na tabela anterior.

|Parâmetro|Objetivo|
|--|--|
|Região|Região do Azure - criação e publicação têm diferentes regiões|
|appID|ID da aplicação LUIS utilizado na rota de URL e encontrado no dashboard de aplicações|
|p|texto de expressão enviado a partir do aplicativo de cliente, como o bot de bate-papo|

## <a name="failover-regions"></a>Regiões de failover

Cada região tem uma região secundária para fazer failover para. O failover da Europa na Europa e na Austrália faz failover dentro da Austrália.

Regiões de criação têm [regiões de failover emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-concebidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
