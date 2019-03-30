---
title: O que é a API de Pesquisa Visual do Bing?
titleSuffix: Azure Cognitive Services
description: A Pesquisa Visual do Bing disponibiliza detalhes ou informações sobre imagens, como imagens semelhantes ou origens de compras.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 8bcb0372ebb60ac3a46cf06bf85322b288e153ba
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630212"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API de Pesquisa Visual do Bing?

A API de pesquisa Visual do Bing devolve informações para uma imagem. Pode carregar uma imagem ou fornecer um URL para um. Insights são imagens visualmente similares, fontes de compras, páginas da Web que incluem a imagem e muito mais. Insights devolvidos pela API de pesquisa Visual do Bing são similares às apresentadas no Bing.com/images.

Se utilizar o [API de pesquisa de imagens do Bing](../bing-image-search/overview.md), pode utilizar os tokens de informações dos resultados da pesquisa essa API para a sua pesquisa Visual do Bing em vez de carregar uma imagem.

> [!IMPORTANT]
> Se obtiver as informações de imagem usando a API de pesquisa de imagens Bing, considere mudar para a API Visual do Bing Search, que fornece informações mais abrangentes.

## <a name="insights"></a>Informações

Pode detetar as seguintes informações ao utilizar a pesquisa Visual do Bing:

| Recomendações personalizadas                              | Descrição |
|--------------------------------------|-------------|
| Imagens visualmente similares              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente semelhantes            | Produtos visualmente semelhantes para o produto mostrado.            |
| Origens de compras                     | Lugares onde pode adquirir o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outras pessoas ou que são baseadas no conteúdo da imagem.            |
| Páginas da Web que incluem a imagem     | Páginas da Web que incluem a imagem de entrada.            |
| Receitas                              | Páginas da Web que incluem receitas para tornar o dish mostrado na imagem de entrada.            |

Além das informações, pesquisa Visual do Bing devolve uma variedade de termos (ou seja, etiquetas) derivado a partir da imagem de entrada. As etiquetas permitem aos utilizadores explorar os conceitos da imagem. Por exemplo, se a imagem de entrada for de um famoso famoso, uma das etiquetas pode ser o nome do famoso, outra etiqueta pode ser desporto. Em alternativa, se a imagem de entrada for de uma pizza da apple, as etiquetas podem ser circular da Apple, empadas e as sobremesas.

Resultados de pesquisa Visual do Bing também incluem delimitação de caixas de regiões de interesse na imagem. Por exemplo, se a imagem contém vários celebridades, os resultados podem incluir delimitação de caixas para cada um das celebridades reconhecidas. Em alternativa, se Bing reconhece um produto ou vestuário na imagem, o resultado pode incluir uma caixa delimitadora para o item reconhecido.

## <a name="workflow"></a>Fluxo de trabalho

A API de pesquisa Visual do Bing é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Pode utilizar a API REST ou o SDK para o serviço.

1. Criar uma [conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para aceder às APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/). Pode obter a chave de subscrição a partir da [portal do Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account#access-your-resource) depois de criar a sua conta, ou [Web site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) após a ativação de uma avaliação gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válidos.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

Em primeiro lugar, experimente a API de pesquisa Visual do Bing [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
A demonstração mostra como pode rapidamente personalizar uma consulta de pesquisa e fazer uma busca na web para imagens.

Para começar rapidamente com o primeiro pedido, veja os inícios rápidos: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Consulte também

* O [imagens - pesquisa Visual](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) referência descreve as definições e informações sobre os pontos de extremidade, solicitar a cabeçalhos, as respostas, e os resultados de pesquisa de parâmetros de consulta que pode utilizar a pedido baseada em imagem.

* O [a API de pesquisa do Bing utilizar e apresentam os requisitos](../bing-web-search/use-display-requirements.md) especificar utiliza aceitável do conteúdo e informações obtidas por meio de APIs de pesquisa do Bing.
