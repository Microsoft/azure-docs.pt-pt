---
title: Exemplos de introspeções Bing - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Este artigo contém exemplos de como a Bing Visual Search pode usar e exibir insights de imagem sobre Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 3d4e9bf67aa1d3df815c674fdc1e2019f68b4a60
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086708"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de utilização de insights Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Este artigo contém exemplos de como bing pode usar e exibir insights de imagem em Bing.com.

## <a name="pagesincluding-insight-example"></a>Páginas Incluindo exemplo de insight

O seguinte exibe um link para a primeira página web e permite que o utilizador expanda e destrua a lista de outras páginas web que incluem a imagem:

![Páginas expandidas, incluindo](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemplo de insight de ShoppingSources

O seguinte mostra como o Bing pode exibir fontes de compras para produtos vistos na imagem:

![Fontes de compras](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemplo de insight visualSearch

O seguinte mostra como bing pode exibir imagens visualmente semelhantes (ver **imagens relacionadas** no exemplo):

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insight de receitas

O seguinte mostra como Bing pode exibir receitas para os alimentos mostrados na imagem. O exemplo permite ao utilizador saber que existem receitas disponíveis:

![Receitas e páginas, incluindo](./media/recipes-pages-including.PNG)

 E fornece o link para as receitas quando o utilizador expande a lista:

![Páginas de receitas expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelacionadosSearches exemplo de insight

O seguinte mostra como Bing pode exibir pesquisas relacionadas de imagens feitas por outros. Se o utilizador clicar na imagem, o utilizador é levado para a página de resultados de pesquisa Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas por imagens](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemplo de insight de entidade

O seguinte mostra como bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostrada na imagem. Se o utilizador clicar no link da entidade, o utilizador é levado para a página de resultados de pesquisa Bing.com para a entidade:

![Entidade mostrada na imagem](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibindo outras ideias que o utilizador pode explorar

O seguinte mostra como bing pode exibir outras informações sobre a imagem que o utilizador pode explorar.

![Explore outros insights sobre a imagem](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Caixas de delimitação e pontos quentes

As etiquetas não padrão incluem a caixa de delimitação que identifica a área de interesse na imagem a que a etiqueta se aplica. Se a caixa de delimitação não identificar toda a imagem, utilize a caixa de delimitação para criar um ponto quente na imagem. O utilizador pode clicar no ponto quente para obter informações relacionadas com o conteúdo encontrado sob o ponto quente (ou retângulo). Por exemplo, se a imagem for uma imagem de alta moda, os resultados podem conter tags (e caixas de delimitação) para acessórios mostrados na imagem, como uma bolsa, joias, cachecóis, etc. O exemplo a seguir mostra um retângulo de ponto quente para os óculos de sol mostrados na imagem:

![Caixa de delimitação e ponto quente](./media/click-to-search.PNG)

## <a name="next-steps"></a>Passos seguintes

Para começar com o seu primeiro pedido, consulte os quickstarts:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)





