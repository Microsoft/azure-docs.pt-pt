---
title: Exemplos de informações do Bing-Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo contém exemplos de como Pesquisa Visual do Bing pode usar e exibir informações de imagem no Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111634"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de uso do Bing insights

Este artigo contém exemplos de como o Bing pode usar e exibir informações de imagem no Bing.com.

## <a name="pagesincluding-insight-example"></a>Exemplo do PagesIncluding Insight

O seguinte exibe um link para a primeira página da Web e permite que o usuário expanda e recolha a lista de outras páginas da Web que incluem a imagem:

![Páginas expandidas, incluindo](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemplo do ShoppingSources Insight

O código a seguir mostra como o Bing pode exibir fontes de compras para produtos vistos na imagem:

![Fontes de compras](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemplo do VisualSearch Insight

Veja a seguir como o Bing pode exibir imagens visualmente semelhantes (consulte as **imagens relacionadas** no exemplo):

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insights de receitas

O seguinte mostra como o Bing pode exibir receitas para a comida mostrada na imagem. O exemplo permite que o usuário saiba que há receitas disponíveis:

![Receitas e páginas, incluindo](./media/recipes-pages-including.PNG)

 E fornece o link para as receitas quando o usuário expande a lista:

![Páginas de receitas expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Exemplo do RelatedSearches Insight

O seguinte mostra como o Bing pode exibir pesquisas relacionadas de imagens feitas por outras pessoas. Se o usuário clicar na imagem, o usuário será levado para a página de resultados da pesquisa do Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas a imagens](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemplo do Entity Insight

O seguinte mostra como o Bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostrada na imagem. Se o usuário clicar no link de entidade, o usuário será levado para a página de resultados da pesquisa do Bing.com para a entidade:

![Entidade mostrada na imagem](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibindo outras informações que o usuário pode explorar

O seguinte mostra como o Bing pode exibir outras informações sobre a imagem que o usuário pode explorar.

![Explorar outras informações sobre a imagem](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Caixas delimitadoras e pontos de acesso

Marcas não padrão incluem a caixa delimitadora que identifica a área de interesse na imagem à qual a marca se aplica. Se a caixa delimitadora não identificar a imagem inteira, use a caixa delimitadora para criar um ponto de acesso na imagem. O usuário pode clicar no ponto de acesso para obter informações relacionadas ao conteúdo encontrado no ponto de acesso (ou retângulo). Por exemplo, se a imagem for uma imagem de alto nível, os resultados poderão conter marcas (e caixas delimitadoras) para acessórios mostrados na imagem, como bolsa, joia, Scarfs e assim por diante. O exemplo a seguir mostra um retângulo de ponto de acesso para o óculos mostrado na imagem:

![Caixa delimitadora e ponto de acesso](./media/click-to-search.PNG)

## <a name="next-steps"></a>Passos seguintes

Para começar a usar sua primeira solicitação, consulte os guias de início [C#](quickstarts/csharp.md) rápido: | [Java](quickstarts/java.md) | [node. js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





