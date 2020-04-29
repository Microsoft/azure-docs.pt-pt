---
title: Exemplos de insights bing - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Este artigo contém exemplos de como bing Visual Search pode usar e exibir insights de imagem sobre Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74111634"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de utilização de insights bing

Este artigo contém exemplos de como bing pode usar e exibir insights de imagem sobre Bing.com.

## <a name="pagesincluding-insight-example"></a>PáginasIncluindo o exemplo de insight

O seguinte exibe um link para a primeira página web e permite ao utilizador expandir e colapsar a lista de outras páginas web que incluem a imagem:

![Páginas expandidas, incluindo](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemplo de insight de Fontes de Compras

O seguinte mostra como bing pode exibir fontes de compras para produtos vistos na imagem:

![Fontes de compras](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemplo de insight de VisualSearch

O seguinte mostra como Bing pode exibir imagens visualmente semelhantes (ver **imagens relacionadas** no exemplo):

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insight de receitas

O seguinte mostra como Bing pode exibir receitas para a comida mostrada na imagem. O exemplo permite ao utilizador saber que existem receitas disponíveis:

![Receitas e páginas, incluindo](./media/recipes-pages-including.PNG)

 E fornece o link para as receitas quando o utilizador expande a lista:

![Páginas de receitas expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelacionadoSPesquisas exemplo de insight

O seguinte mostra como Bing pode exibir pesquisas relacionadas de imagens feitas por outros. Se o utilizador clicar na imagem, o utilizador é levado para a página de resultados de pesquisa Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas por imagens](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemplo de insight da entidade

O seguinte mostra como bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostradas na imagem. Se o utilizador clicar no link da entidade, o utilizador é levado para a página de resultados de pesquisa Bing.com para a entidade:

![Entidade mostrada na imagem](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibindo outras ideias que o utilizador pode explorar

O seguinte mostra como bing pode exibir outras informações sobre a imagem que o utilizador pode explorar.

![Explore outras ideias sobre a imagem](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Caixas de delimitação e pontos quentes

As etiquetas não predefinidas incluem a caixa de delimitação que identifica a área de interesse na imagem a que a etiqueta se aplica. Se a caixa de delimitação não identificar toda a imagem, utilize a caixa de delimitação para criar um ponto quente na imagem. O utilizador pode clicar no ponto quente para obter informações relacionadas com o conteúdo encontrado sob o ponto quente (ou retângulo). Por exemplo, se a imagem for uma imagem de alta moda, os resultados podem conter etiquetas (e caixas de delimitação) para acessórios mostrados na imagem, tais como uma bolsa, joias, cachecóis, e assim por diante. O exemplo seguinte mostra um retângulo de ponto quente para os óculos de sol mostrados na imagem:

![Caixa de delimitação e ponto quente](./media/click-to-search.PNG)

## <a name="next-steps"></a>Passos seguintes

Para começar com o seu primeiro pedido, veja os quickstarts: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





