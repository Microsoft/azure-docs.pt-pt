---
title: Ponto final de Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Crie experiências de pesquisa personalizadas para tópicos que lhe interessam. Os utilizadores vêem os resultados da pesquisa adaptados ao conteúdo que lhes interessa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 80215a22c5af3698aee6719f52e42c457220b7f9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367465"
---
# <a name="custom-search"></a>Pesquisa Personalizada

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)
Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa que podem apresentar conteúdos irrelevantes.

## <a name="custom-search-endpoint"></a>Ponto de final de pesquisa personalizado
Para obter resultados usando a API de Pesquisa Personalizada Bing, envie um `GET` pedido para o seguinte ponto final. Utilize os cabeçalhos e os parâmetros URL para definir especificações adicionais.

Ponto final: Devolve as sugestões de pesquisa como resultados JSON relevantes para a entrada do utilizador definida por `?q=""` .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Por exemplo, que descrevem como configurar fontes de pesquisa personalizadas, consulte o [tutorial](./tutorials/custom-search-web-page.md). Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Custom Search API v7.](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)

## <a name="custom-search-response-json"></a>Resposta personalizada JSON
Um pedido de pesquisa personalizado retorna os resultados como objetos JSON, ver [objetos de resposta](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Autosuggest personalizado
A API autosusuggest personalizada permite-lhe enviar um termo de consulta parcial de pesquisa para Bing e obter de volta uma lista de consultas sugeridas que você pode configurar. Com o Custom Autosuggest, adiciona sugestões devolvidas pela API e especifica opcionalmente se deve incluir sugestões geradas por Bing.

## <a name="custom-autosuggest-endpoint"></a>Ponto final autosuggest personalizado
Para solicitar sugestões de consulta personalizada, envie um pedido GET para:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Para obter informações sobre a definição de sugestões personalizadas, consulte [sugestões de pesquisa personalizadas.](define-custom-suggestions.md)

## <a name="custom-image-search"></a>Pesquisa de imagem personalizada
A API de Pesquisa de Imagem Personalizada permite-lhe enviar uma consulta de pesquisa para Bing e recuperar uma lista de imagens relevantes a partir da sua instância de Pesquisa Personalizada.

## <a name="custom-image-search-endpoint"></a>Ponto final de pesquisa de imagem personalizada
Para solicitar imagens a partir da sua instância de Pesquisa Personalizada, envie um pedido GET para o seguinte URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Para obter informações sobre a configuração de uma instância de pesquisa personalizada, consulte [configurar a sua experiência de pesquisa personalizada.](./define-your-custom-view.md)

## <a name="next-steps"></a>Passos seguintes
As **APIs de Bing suportam** ações de pesquisa que devolvem resultados de acordo com o seu tipo. Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos finais suportam consultas que retornem uma linguagem e/ou localização específicas por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para, por exemplo, de pedidos básicos que utilizam a API de Pesquisa Personalizada, consulte [o Acesso Rápido de Pesquisa Personalizada](/azure/cognitive-services/bing-custom-search/)