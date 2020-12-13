---
title: As regiões editoriais & pontos finais - LUIS
description: A região especificada no portal Azure é a mesma onde vai publicar a app LUIS e é gerado um URL de ponto final para esta mesma região.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 5f65c5e1e1c8d306a70be3fdd7a07f18f8ebbd9f
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368652"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de autoria e publicação e as chaves associadas

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Três regiões de autoria são apoiadas por portais LUIS correspondentes. Para publicar uma aplicação LUIS em mais de uma região, precisará de, pelo menos, uma chave por região.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de autoria do LUIS
Existem três portais de autoria LUIS, baseados na região. Tem de criar e publicar na mesma região.

|LUIS|Região de autoria|Nome da região de Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] |U.S.<br>não a Europa<br>não a Austrália| `westus`|
|[au.luis.ai][au.luis.ai] |Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] |Europa|`westeurope`|

As regiões [autorais emparelharam regiões de insuporte.](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões editoriais e recursos Azure
A aplicação é publicada em todas as regiões associadas aos recursos LUIS adicionados no portal do LUIS. Por exemplo, para uma aplicação criada no [www.luis.ai][www.luis.ai], se criar um recurso LUIS ou Serviço Cognitivo no **westus** e [adicioná-lo à app como recurso](luis-how-to-azure-subscription.md), a aplicação é publicada naquela região.

## <a name="public-apps"></a>Aplicativos públicos
Uma aplicação pública é publicada em todas as regiões para que um utilizador com uma chave de recursos LUIS baseada na região possa aceder à aplicação em qualquer região que esteja associada à sua chave de recursos.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>As regiões editoriais estão ligadas a regiões de autoria

A aplicação da região de autoria só pode ser publicada numa região de publicação correspondente. Se a aplicação se encontrar atualmente na região de criação errada, exporte-a e importe-a para a região de criação correta da região de publicação.

As aplicações LUIS criadas https://www.luis.ai podem ser publicadas em todos os pontos finais, exceto nas regiões [europeia](#publishing-to-europe) e [australiana.](#publishing-to-australia)

## <a name="publishing-to-europe"></a>Publicação na Europa

Para publicar nas regiões europeias, cria apenas aplicações https://eu.luis.ai LUIS. Se tentar publicar em qualquer outro lugar usando uma chave na região da Europa, a LUIS exibe uma mensagem de aviso. Em vez disso, use https://eu.luis.ai . As aplicações LUIS criadas em [https://eu.luis.ai][eu.luis.ai] não migram automaticamente para outras regiões. Exporte e, em seguida, importe a app LUIS para a migrar.

## <a name="europe-publishing-regions"></a>Regiões editoriais da Europa

 Região global | Autoria da região da API & site de autoria| Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sul do Reino Unido<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicação para a Austrália

Para publicar nas regiões australianas, cria apenas aplicações https://au.luis.ai LUIS. Se tentar publicar em qualquer outro lugar usando uma chave na região australiana, a LUIS exibe uma mensagem de aviso. Em vez disso, use https://au.luis.ai . As aplicações LUIS criadas em [https://au.luis.ai][au.luis.ai] não migram automaticamente para outras regiões. Exporte e, em seguida, importe a app LUIS para a migrar.

## <a name="australia-publishing-regions"></a>Austrália publicando regiões

 Região global | Autoria da região da API & site de autoria| Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publicação noutras regiões

Para publicar para outras regiões, cria apenas aplicações [https://www.luis.ai](https://www.luis.ai) LUIS.

## <a name="other-publishing-regions"></a>Outras regiões editoriais

 Região global | Autoria da região da API & site de autoria| Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| Norte da África do Sul<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Leste<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Central<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Norte<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Sul<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Oeste<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Pontos Finais

Saiba mais sobre os [pontos finais de autoria e previsão](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiões de ativação pós-falha

Cada região tem uma região secundária para não conseguir. A Europa falha dentro da Europa e a Austrália falha dentro da Austrália.

As regiões [autorais emparelharam regiões de insuporte.](../../best-practices-availability-paired-regions.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-construídas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai