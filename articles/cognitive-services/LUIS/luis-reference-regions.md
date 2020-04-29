---
title: Regiões editoriais & pontos finais - LUIS
description: A região especificada no portal Azure é a mesma onde irá publicar a app LUIS e é gerado um URL de ponto final para esta mesma região.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292092"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de autoria e publicação e as chaves associadas

Três regiões de autoria são apoiadas pelos correspondentes portais LUIS. Para publicar uma app LUIS para mais de uma região, você precisa de pelo menos uma chave por região.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>REGIões autorais LUIS
Existem três portais de autoria LUIS, baseados na região. Tem de criar e publicar na mesma região.

|LUIS|Região de autoria|Nome da região de Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|U.S.<br>não a Europa<br>não a Austrália| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

As regiões autorais [emparelharam regiões de fail-over.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões editoriais e recursos Azure
A aplicação é publicada em todas as regiões associadas aos recursos do LUIS adicionados no portal LUIS. Por exemplo, para uma aplicação criada na [www.luis.ai][www.luis.ai], se criar um recurso LUIS ou Serviço Cognitivo em **Westus** e [adicioná-lo à app como recurso](luis-how-to-azure-subscription.md), a aplicação é publicada naquela região.

## <a name="public-apps"></a>Aplicativos públicos
Uma aplicação pública é publicada em todas as regiões para que um utilizador com uma chave de recursos LUIS baseada na região possa aceder à app em qualquer região que esteja associada à sua chave de recursos.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões editoriais estão ligadas a regiões autorais

A aplicação da região de autoria só pode ser publicada numa região editorial correspondente. Se a sua aplicação estiver atualmente na região de autor errada, exporte a app e importe-a para a região de autor correta para a sua região editorial.

As aplicações https://www.luis.ai LUIS criadas podem ser publicadas em todos os pontos finais, exceto as regiões [europeia](#publishing-to-europe) e [australiana.](#publishing-to-australia)

## <a name="publishing-to-europe"></a>Publicação para a Europa

Para publicar nas regiões europeias, https://eu.luis.ai cria apenas aplicações LUIS. Se tentar publicar em qualquer outro lugar usando uma chave na região da Europa, a LUIS exibe uma mensagem de aviso. Em vez https://eu.luis.aidisso, use. As aplicações [https://eu.luis.ai][eu.luis.ai] LUIS criadas não migram automaticamente para outras regiões. Exportar e, em seguida, importar a app LUIS para migrar.

## <a name="europe-publishing-regions"></a>Regiões editoriais da Europa

 Região global | Autor da região da API & site de autoria| Região de consulta de & editorial<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa do Norte<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa ocidental<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sul do Reino Unido<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicação para a Austrália

Para publicar nas regiões australianas, https://au.luis.ai cria apenas aplicações LUIS. Se tentar publicar em qualquer outro lugar usando uma chave na região australiana, a LUIS exibe uma mensagem de aviso. Em vez https://au.luis.aidisso, use. As aplicações [https://au.luis.ai][au.luis.ai] LUIS criadas não migram automaticamente para outras regiões. Exportar e, em seguida, importar a app LUIS para migrar.

## <a name="australia-publishing-regions"></a>Regiões editoriais da Austrália

 Região global | Autor da região da API & site de autoria| Região de consulta de & editorial<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publicação para outras regiões

Para publicar nas outras regiões, [https://www.luis.ai](https://www.luis.ai) cria apenas aplicações LUIS.

## <a name="other-publishing-regions"></a>Outras regiões editoriais

 Região global | Autor da região da API & site de autoria| Região de consulta de & editorial<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| África do Sul Norte<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Leste<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Sudeste<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Central<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Norte<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Sul<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Oeste<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A.Oeste 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Pontos Finais

Saiba mais sobre os [pontos finais](developer-reference-resource.md)de autoria e previsão.

## <a name="failover-regions"></a>Regiões de ativação pós-falha

Cada região tem uma região secundária para não conseguir. A Europa falha dentro da Europa e a Austrália falha dentro da Austrália.

As regiões autorais [emparelharam regiões de fail-over.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-construídas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
