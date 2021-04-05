---
title: As regiões editoriais & pontos finais - LUIS
description: A região especificada no portal Azure é a mesma onde vai publicar a app LUIS e é gerado um URL de ponto final para esta mesma região.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98681612"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Regiões de autoria e publicação e as chaves associadas

As regiões de autoria do LUIS são apoiadas pelo portal LUIS. Para publicar uma aplicação LUIS em mais de uma região, precisará de, pelo menos, uma chave por região.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de autoria do LUIS

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

O LUIS tem um portal que pode utilizar independentemente da região, [www.luis.ai.](https://www.luis.ai) Deve ainda ser autor e publicado na mesma região.

As regiões [autorais emparelharam regiões de repesta](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões editoriais e recursos Azure

A aplicação é publicada em todas as regiões associadas aos recursos LUIS adicionados no portal do LUIS. Por exemplo, para uma aplicação criada no [www.luis.ai][www.luis.ai], se criar um recurso LUIS ou Serviço Cognitivo no **westus** e [adicioná-lo à app como recurso](luis-how-to-azure-subscription.md), a aplicação é publicada naquela região.

## <a name="public-apps"></a>Aplicativos públicos
Uma aplicação pública é publicada em todas as regiões para que um utilizador com uma chave de recursos LUIS baseada na região possa aceder à aplicação em qualquer região que esteja associada à sua chave de recursos.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>As regiões editoriais estão ligadas a regiões de autoria

A aplicação da região de autoria só pode ser publicada numa região de publicação correspondente. Se a aplicação se encontrar atualmente na região de criação errada, exporte-a e importe-a para a região de criação correta da região de publicação.

> [!NOTE]
> As aplicações LUIS criadas https://www.luis.ai podem agora ser publicadas em todos os pontos finais, incluindo as regiões [europeia](#publishing-to-europe) e [australiana.](#publishing-to-australia)

## <a name="publishing-to-europe"></a>Publicação na Europa

 Região global | Autoria da região da API | Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| Europa | `westeurope`| França Central<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa do Norte<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa Ocidental<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Sul do Reino Unido<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicação para a Austrália

 Região global | Autoria da região da API | Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| Austrália | `australiaeast` | Leste da Austrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Outras regiões editoriais

 Região global | Autoria da região da API | Publicação & região de consulta<br>`API region name`   |  Formato URL de ponto final   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| Norte da África do Sul<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Leste<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia do Sul Central<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Emirados Árabes Unidos do Norte<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Central<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Leste 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Norte<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Sul<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Centro-Oeste<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | E.U.A. Oeste 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Pontos Finais

Saiba mais sobre os [pontos finais de autoria e previsão](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiões de ativação pós-falha

Cada região tem uma região secundária para não conseguir. A Europa falha dentro da Europa e a Austrália falha dentro da Austrália.

As regiões [autorais emparelharam regiões de insuporte.](../../best-practices-availability-paired-regions.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de entidades pré-construídas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai