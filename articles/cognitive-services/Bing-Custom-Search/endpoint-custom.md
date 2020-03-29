---
title: Ponto final de Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Crie experiências de pesquisa personalizadas para tópicos com os seus trabalhos. Os utilizadores vêem os resultados da pesquisa adaptados ao conteúdo com que se preocupam.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072790"
---
# <a name="custom-search"></a>Pesquisa Personalizada
Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa que podem apresentar conteúdos irrelevantes.

## <a name="custom-search-endpoint"></a>Ponto final de pesquisa personalizado
Para obter resultados utilizando a API `GET` de pesquisa personalizada bing, envie um pedido para o seguinte ponto final. Utilize os cabeçalhos e os parâmetros url para definir especificações adicionais.

Ponto final: Devolve sugestões de pesquisa como resultados JSON que `?q=""`são relevantes para a entrada do utilizador definida por .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Por exemplo, que descrevem como configurar fontes de pesquisa personalizadas, consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) de Pesquisa Personalizada bing.

## <a name="custom-search-response-json"></a>Resposta de pesquisa personalizada JSON
Um pedido de pesquisa personalizado devolve resultados como objetos JSON, ver [objetos resposta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Autosuggest personalizado
A API Custom Autosuggest permite-lhe enviar um termo parcial de consulta de pesquisa para Bing e obter de volta uma lista de consultas sugeridas que você pode configurar. Com a Custom Autosuggest, adiciona sugestões devolvidas pela API e especifica opcionalmente se deve incluir sugestões geradas pelo Bing.

## <a name="custom-autosuggest-endpoint"></a>Ponto final personalizado de autossugestão
Para solicitar sugestões de consulta personalizada, envie um pedido get para:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Para obter informações sobre a definição de sugestões personalizadas, consulte Definir sugestões de [pesquisa personalizadas](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Pesquisa de imagem personalizada
A API de Pesquisa de Imagem Personalizada permite-lhe enviar uma consulta de pesquisa para Bing e obter de volta uma lista de imagens relevantes da sua instância de Pesquisa Personalizada.

## <a name="custom-image-search-endpoint"></a>Ponto final de pesquisa de imagem personalizado
Para solicitar imagens da sua instância de Pesquisa Personalizada, envie um pedido GET para o seguinte URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Para obter informações sobre a configuração de uma instância de pesquisa personalizada, consulte [Configurar a sua experiência](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view)de pesquisa personalizada .

## <a name="next-steps"></a>Passos seguintes
As APIs **bing** suportam ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos finais suportam consultas que devolvem uma linguagem e/ou localização específicas por longitude, latitude e raio de busca.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para exemplos de pedidos básicos usando a API de pesquisa personalizada, consulte [Quick-starts de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
