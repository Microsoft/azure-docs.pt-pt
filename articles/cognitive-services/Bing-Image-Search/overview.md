---
title: O que é a API de Pesquisa de Imagens do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagens do Bing permite-lhe utilizar as capacidades cognitivas de pesquisa de imagens do Bing na sua aplicação. Com o envio de consultas de pesquisa de utilizadores com a API, pode obter e apresentar imagens relevantes e de alta qualidade semelhantes às Imagens do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1240c717f0e0fe086be81ed27837c224bc683218
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593472"
---
# <a name="what-is-the-bing-image-search-api"></a>O que é a API de Pesquisa de Imagens do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Pesquisa de Imagem Bing permite-lhe utilizar as capacidades de pesquisa de imagem de Bing na sua aplicação. Ao enviar consultas de pesquisa para a API, você pode obter imagens de alta qualidade semelhantes a [bing.com/images](https://www.bing.com/images).

Enquanto a API de Pesquisa de Imagem Bing fornece resultados de pesquisa apenas de imagem, pode combinar ou utilizar as outras APIs de [pesquisa de Bing](../bing-web-search/bing-api-comparison.md) disponíveis para encontrar muitos tipos de conteúdo na web.

## <a name="bing-image-search-features"></a>Funcionalidades da Pesquisa de Imagens do Bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](./concepts/bing-image-search-sending-queries.md) | Melhore a sua experiência de aplicação utilizando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados de imagem](./concepts/bing-image-search-get-images.md)                       | Filtre as imagens que o Bing devolve ao editar os parâmetros de consulta.                                                                                                       |
| [Recortar, redimensionar e apresentar miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Edite e apresente visualizações de miniaturas para as imagens devolvidas pela Pesquisa de Imagens do Bing.                                                                                      |
| [Dinamizar e expandir consultas de pesquisa do utilizador](./concepts/bing-image-search-sending-queries.md)               | Expanda as capacidades de pesquisa ao incluir e apresentar os termos de pesquisa para consultas sugeridos pelo Bing.                                                                    |
| [Obter imagens populares](trending-images.md)                                                                     | Personalize uma pesquisa para obter imagens populares em todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Imagens do Bing é um serviço Web RESTful fácil de chamar a partir de qualquer linguagem que possa fazer pedidos e analisar as respostas JSON. Pode utilizar o serviço através da [API REST](./quickstarts/csharp.md) ou o [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envie um pedido para a API com uma [consulta de pesquisa](./concepts/bing-image-search-sending-queries.md) válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Em primeiro lugar, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) da API de Pesquisa de Imagens do Bing.
Esta demonstração mostra como pode rapidamente personalizar uma consulta de pesquisa e fazer uma busca de imagens na web.

Para começar rapidamente com o primeiro pedido da API, pode aprender a:

* [Enviar consultas de pesquisa para o Bing](./quickstarts/csharp.md) com a API REST ou
* [Pedir e filtrar](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) as imagens que o Bing devolve utilizando o SDK.

## <a name="see-also"></a>Ver também

* [Detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) para as APIs de pesquisa de Bing. 

* A secção de referência [de pesquisa de imagem Bing API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) contém informações sobre os pontos finais da API, cabeçalhos, respostas API e parâmetros de consulta.

* Os [Requisitos de apresentação e utilização do Bing](../bing-web-search/use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.

* As [imagens de Obtenção da web com o artigo da Bing Image Search API](./concepts/bing-image-search-get-images.md) descrevem como pesquisar e obter imagens da web.

* O [artigo de Envio e Trabalho com consultas de pesquisa](./concepts/bing-image-search-sending-queries.md) descreve como fazer, personalizar e pivô consultas de pesquisa.

* Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.