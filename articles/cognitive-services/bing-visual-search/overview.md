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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446660"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API de Pesquisa Visual do Bing?

A API de Pesquisa Visual Bing devolve insights para uma imagem. Pode fazer o upload de uma imagem ou fornecer um URL a uma. Insights são imagens visualmente semelhantes, fontes de compras, páginas web que incluem a imagem, e muito mais. Insights devolvidos pela API de Pesquisa Visual bing são semelhantes aos mostrados no Bing.com/images. 

Se utilizar a API de Pesquisa de [Imagem Bing,](../bing-image-search/overview.md)pode utilizar fichas de insights a partir dos resultados de pesquisa da API para a sua Pesquisa Visual Bing em vez de enviar uma imagem.

> [!IMPORTANT]
> Se obtém insights de imagem utilizando a API de Pesquisa de Imagem Bing, considere mudar para a API de Pesquisa Visual Bing, que fornece insights mais abrangentes.

## <a name="insights"></a>Informações

Pode descobrir as seguintes informações utilizando bing Visual Search:

| Informações                              | Descrição |
|--------------------------------------|-------------|
| Imagens visualmente semelhantes              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente similares            | Produtos visualmente semelhantes ao produto mostrado.            |
| Fontes de compras                     | Locais onde pode comprar o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outros ou que são baseadas no conteúdo da imagem.            |
| Páginas web que incluem a imagem     | Páginas web que incluem a imagem de entrada.            |
| Receitas                              | Páginas web que incluem receitas para fazer o prato mostrado na imagem de entrada.            |
| Entidades                             | Pessoas conhecidas, lugares e coisas. |

Além de insights, bing Visual Search retorna uma variedade de termos (isto é, tags) derivados da imagem de entrada. As tags permitem que os utilizadores explorem conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for de um atleta famoso, uma das etiquetas pode ser o nome do atleta, outra etiqueta pode ser o Sports. Ou, se a imagem de entrada for de uma torta de maçã, as etiquetas podem ser Tortas de Maçã, Tortas e Sobremesas.

Os resultados da Bing Visual Search também incluem caixas de delimitação para regiões de interesse na imagem. Por exemplo, se a imagem contiver várias celebridades, os resultados podem incluir caixas de delimitação para cada uma das celebridades reconhecidas. Ou, se bing reconhecer um produto ou roupa na imagem, o resultado pode incluir uma caixa de delimitação para o item reconhecido.

## <a name="workflow"></a>Fluxo de trabalho

O Bing Visual Search API é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar a JSON. Pode utilizar a API REST ou o SDK para o serviço.

1. Crie uma [conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para aceder às APIs de Pesquisa bing. Se não tiver uma subscrição Azure, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/) Pode obter a sua chave de subscrição no [portal Azure](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de criar a sua conta, ou [o site Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) depois de ativar um teste gratuito.
2. Envie um pedido à API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)Bing Visual Search API.
A demonstração mostra como pode personalizar rapidamente uma consulta de pesquisa e vasculhar a web para obter imagens.

Para começar rapidamente com o seu primeiro pedido, veja os quickstarts: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Consulte também

* A referência [Images - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) descreve definições e informações sobre os pontos finais, cabeçalhos de pedido, respostas e parâmetros de consulta que pode utilizar para solicitar resultados de pesquisa baseados em imagem.

* Os requisitos de [utilização e exibição](../bing-web-search/use-display-requirements.md) de API de pesquisa bing e exespecifiquem utilizações aceitáveis do conteúdo e informações obtidas através das APIs de pesquisa bing.

* Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.