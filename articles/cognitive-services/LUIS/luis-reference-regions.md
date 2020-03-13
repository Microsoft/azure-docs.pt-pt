---
title: Regiões editoriais e pontos finais - LUIS
description: A região especificada no portal Azure é a mesma onde irá publicar a app LUIS e é gerado um URL de ponto final para esta mesma região.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 097f3078b54b014dc646d144f3532e20236a9e4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219755"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de autoria e publicação e as chaves associadas

Três regiões de autoria são apoiadas pelos correspondentes portais LUIS. Para publicar uma aplicação do LUIS mais do que uma região, tem pelo menos uma chave por região.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>REGIões autorais LUIS
Existem três portais de autoria LUIS, baseados na região. Tem de criar e publicar na mesma região.

|LUIS|Região de criação|Nome da região de Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|As<br>não Europa<br>não Austrália| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

As regiões autorais [emparelharam regiões de fail-over.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões editoriais e recursos Azure
A aplicação for publicada para todas as regiões associadas com os recursos de LUIS, adicionados no portal do LUIS. Por exemplo, para uma aplicação criada na [www.luis.ai][www.luis.ai], se criar um recurso LUIS ou Serviço Cognitivo em **Westus** e [adicioná-lo à app como recurso](luis-how-to-azure-subscription.md), a aplicação é publicada naquela região.

## <a name="public-apps"></a>Aplicações públicas
Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região é associada à sua chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões editoriais estão ligadas a regiões autorais

Só pode ser publicada a aplicação de região de criação para odpovídající publicar região. Se a aplicação está atualmente na região errada de criação, exportar a aplicação e importe-o para a região de criação correta para a sua região de publicação.

As aplicações LUIS criadas em https://www.luis.ai podem ser publicadas em todos os pontos finais, exceto as regiões [europeia](#publishing-to-europe) e [australiana.](#publishing-to-australia)

## <a name="publishing-to-europe"></a>Publicação para a Europa

Para publicar nas regiões europeias, cria aplicações LUIS apenas na https://eu.luis.ai. Se tentar publicar em qualquer lugar que outra com uma chave na região da Europa, LUIS exibe uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. As aplicações LUIS criadas em [https://eu.luis.ai][eu.luis.ai] não migram automaticamente para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="europe-publishing-regions"></a>Regiões editoriais da Europa

 Região global | Site de autoria da região da API e da autoria| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sul do Reino Unido<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicação da Austrália

Para publicar nas regiões australianas, cria aplicações LUIS apenas https://au.luis.ai. Se tentar publicar em qualquer lugar que outra com uma chave na região australiano, LUIS exibe uma mensagem de aviso. Em vez disso, use https://au.luis.ai. As aplicações LUIS criadas em [https://au.luis.ai][au.luis.ai] não migram automaticamente para outras regiões. Exportar e, em seguida, importe a aplicação do LUIS para migrá-la.

## <a name="australia-publishing-regions"></a>Regiões editoriais da Austrália

 Região global | Site de autoria da região da API e da autoria| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicação para outras regiões

Para publicar nas outras regiões, cria aplicações LUIS apenas em [https://www.luis.ai.](https://www.luis.ai)

## <a name="other-publishing-regions"></a>Outras regiões editoriais

 Região global | Site de autoria da região da API e da autoria| Publicar & consulta de região<br>`API region name`   |  Formato de URL de ponto final   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| África do Sul Norte<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Leste<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Sudeste<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Central<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Norte<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Sul<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Oeste<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A.Oeste 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Pontos Finais

Saiba mais sobre os [pontos finais](developer-reference-resource.md)de autoria e previsão.

## <a name="failover-regions"></a>Regiões de failover

Cada região tem uma região secundária para não conseguir. A Europa falha dentro da Europa e a Austrália falha dentro da Austrália.

As regiões autorais [emparelharam regiões de fail-over.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-construídas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
