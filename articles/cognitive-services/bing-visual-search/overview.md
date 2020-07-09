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
ms.openlocfilehash: 14acebb71538dddfd2421fbb27794f456b6c9c8f
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611514"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API de Pesquisa Visual do Bing?

A API de Pesquisa Visual Bing retorna insights para uma imagem. Pode fazer upload de uma imagem ou fornecer um URL a uma. Os insights são imagens visualmente semelhantes, fontes de compras, páginas web que incluem a imagem, e muito mais. Os insights devolvidos pela API de Pesquisa Visual Bing são semelhantes aos apresentados no Bing.com/images. 

Se utilizar a [API de Pesquisa de Imagem Bing,](../bing-image-search/overview.md)pode utilizar fichas de visão a partir dos resultados de pesquisa da API para a sua Pesquisa Visual Bing em vez de carregar uma imagem.

> [!IMPORTANT]
> Se tiver insights de imagem utilizando a API de Pesquisa de Imagem de Bing, considere mudar para a API de Pesquisa Visual Bing, que fornece insights mais abrangentes.

## <a name="insights"></a>Informações

Pode descobrir as seguintes informações utilizando a Pesquisa Visual Bing:

| Informações                              | Description |
|--------------------------------------|-------------|
| Imagens visualmente semelhantes              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente similares            | Produtos que são visualmente semelhantes ao produto mostrado.            |
| Fontes de compras                     | Locais onde pode comprar o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outros ou que se baseiam no conteúdo da imagem.            |
| Páginas web que incluem a imagem     | Páginas web que incluem a imagem de entrada.            |
| Receitas                              | Páginas web que incluem receitas para fazer o prato mostrado na imagem de entrada.            |
| Entidades                             | Pessoas conhecidas, lugares e coisas. |

Além de insights, bing Visual Search devolve uma variedade de termos (isto é, tags) derivados da imagem de entrada. As tags permitem que os utilizadores explorem conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for de um atleta famoso, uma das etiquetas pode ser o nome do atleta, outra etiqueta pode ser Sports. Ou, se a imagem de entrada é de uma torta de maçã, as etiquetas podem ser Torta de Maçã, Tortas e Sobremesas.

Os resultados da Pesquisa Visual Bing também incluem caixas de delimitação para regiões de interesse na imagem. Por exemplo, se a imagem contiver várias celebridades, os resultados podem incluir caixas de delimitação para cada uma das celebridades reconhecidas. Ou, se Bing reconhecer um produto ou roupa na imagem, o resultado pode incluir uma caixa de delimitação para o item reconhecido.

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa Visual Bing é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar o JSON. Pode utilizar a API REST ou a SDK para o serviço.

1. Crie uma [conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para aceder às APIs de Pesquisa de Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuitamente](https://azure.microsoft.com/free/cognitive-services/).
2. Envie um pedido à API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)Bing Visual Search API .
A demonstração mostra como você pode rapidamente personalizar uma consulta de pesquisa e vasculhar a web para obter imagens.

Para começar rapidamente com o seu primeiro pedido, consulte os quickstarts: [C#](quickstarts/csharp.md)  |  [Java](quickstarts/java.md)  |  [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

## <a name="see-also"></a>Ver também

* A referência [Imagens - Procura Visual](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) descreve definições e informações sobre os pontos finais, pedido de cabeçalhos, respostas e parâmetros de consulta que pode utilizar para solicitar resultados de pesquisa baseados na imagem.

* Os [requisitos de utilização e exibição da API de pesquisa de Bing](../bing-web-search/use-display-requirements.md) especificam utilizações aceitáveis do conteúdo e informações obtidas através das APIs de pesquisa de Bing.

* Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.