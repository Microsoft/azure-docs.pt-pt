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
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446660"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API de Pesquisa Visual do Bing?

O API da Pesquisa Visual do Bing retorna informações para uma imagem. Você pode carregar uma imagem ou fornecer uma URL para uma. As informações são imagens visualmente semelhantes, fontes de compras, páginas da Web que incluem a imagem e muito mais. As informações retornadas pelo API da Pesquisa Visual do Bing são semelhantes àquelas mostradas em Bing.com/images. 

Se você usar o [API de pesquisa de imagem do Bing](../bing-image-search/overview.md), poderá usar tokens de insight dos resultados da pesquisa da API para sua pesquisa visual do Bing em vez de carregar uma imagem.

> [!IMPORTANT]
> Se você receber informações de imagem usando o API de Pesquisa de Imagem do Bing, considere alternar para o API da Pesquisa Visual do Bing, que fornece informações mais abrangentes.

## <a name="insights"></a>Insights

Você pode descobrir as seguintes informações usando Pesquisa Visual do Bing:

| Informações                              | Descrição |
|--------------------------------------|-------------|
| Imagens visualmente semelhantes              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente semelhantes            | Produtos que são visualmente semelhantes ao produto mostrado.            |
| Fontes de compras                     | Locais onde você pode comprar o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outras pessoas ou que se baseiam no conteúdo da imagem.            |
| Páginas da Web que incluem a imagem     | Páginas da Web que incluem a imagem de entrada.            |
| Receitas                              | Páginas da Web que incluem receitas para fazer o Dish mostrado na imagem de entrada.            |
| Entidades                             | Pessoas, lugares e coisas conhecidos. |

Além de informações, Pesquisa Visual do Bing retorna uma variedade de termos (ou seja, marcas) derivados da imagem de entrada. As marcas permitem que os usuários explorem os conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for de um famoso atletas, uma das marcas poderá ser o nome do atletas, outra marca poderá ser esportiva. Ou, se a imagem de entrada for de uma pizza da Apple, as marcas poderão ser pizza da Apple, tortas e sobremesas.

Pesquisa Visual do Bing resultados também incluem caixas delimitadoras para regiões de interesse na imagem. Por exemplo, se a imagem contiver vários celebridades, os resultados poderão incluir caixas delimitadoras para cada um dos celebridades reconhecidos. Ou, se o Bing reconhecer um produto ou roupas na imagem, o resultado poderá incluir uma caixa delimitadora para o item reconhecido.

## <a name="workflow"></a>Fluxo de Trabalho

O API da Pesquisa Visual do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON. Você pode usar a API REST ou o SDK para o serviço.

1. Crie uma [conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para acessar o APIs de pesquisa do Bing. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuitamente](https://azure.microsoft.com/free/). Você pode obter sua chave de assinatura do [portal do Azure](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de criar sua conta ou [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) depois de ativar uma avaliação gratuita.
2. Envie uma solicitação para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)API da pesquisa visual do Bing.
A demonstração mostra como você pode personalizar rapidamente uma consulta de pesquisa e pesquisar a Web para imagens.

Para começar a trabalhar rapidamente com o seu primeiro pedido, veja os inícios rápidos: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Ver também

* A referência de [pesquisa visual de imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) descreve as definições e informações sobre os pontos de extremidade, os cabeçalhos de solicitação, as respostas e os parâmetros de consulta que você pode usar para solicitar resultados de pesquisa baseados em imagem.

* O [pesquisa do Bing uso da API e os requisitos de exibição](../bing-web-search/use-display-requirements.md) especificam usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.

* Visite a [página do hub de API pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.