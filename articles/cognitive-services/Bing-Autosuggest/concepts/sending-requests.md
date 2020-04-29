---
title: Envio de pedidos para a API Bing Autosuggest
titleSuffix: Azure Cognitive Services
description: A API Bing Autosuggest devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa. Saiba mais sobre envio de pedidos.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74072853"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Envio de pedidos para a API Bing Autosuggest.

Se a sua aplicação enviar consultas a qualquer uma das APIs de Pesquisa de Bing, pode utilizar a API Bing Autosuggest para melhorar a experiência de pesquisa dos seus utilizadores. A API Bing Autosuggest devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa. À medida que os caracteres são inseridos numa caixa de pesquisa na sua aplicação, pode apresentar sugestões numa lista de drop-down. Use este artigo para saber mais sobre o envio de pedidos para esta API. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Autosuggest API Endpoint

A **API Bing Autosuggest** inclui um ponto final, que devolve uma lista de consultas sugeridas a partir de um termo de pesquisa parcial.

Para obter consultas sugeridas usando a API Bing, envie um `GET` pedido para o seguinte ponto final. Utilize os cabeçalhos e os parâmetros url para definir especificações adicionais.

**Ponto final:** Devolve sugestões de pesquisa como resultados JSON que são `?q=""`relevantes para a entrada do utilizador definida por .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Autosuggest API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

As APIs **bing** suportam ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON.
Todos os pontos finais suportam consultas que devolvem uma linguagem e/ou localização específicas por longitude, latitude e raio de busca.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Por exemplo, em pedidos básicos utilizando a API Autosuggest, consulte [Autosuggest Quickstarts](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Bing Autosuggest Pedidos de API

> [!NOTE]
> * Os pedidos à Bing Autosuggest API devem utilizar o protocolo HTTPS.

Recomendamos que todos os pedidos tenham origem num servidor. Distribuir a chave como parte de uma aplicação de cliente proporciona mais oportunidades de acesso malicioso a terceiros. Além disso, fazer chamadas a partir de um servidor fornece um único ponto de upgrade para futuras atualizações.

O pedido tem de especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), que contém o termo de pesquisa parcial do utilizador. Embora seja opcional, o pedido deve também especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), que identifica o mercado de onde pretende que os resultados provenham. Para obter uma lista opcional de parâmetros de consulta, veja [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta têm de estar codificados com URL.

O pedido tem de especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Embora seja opcional, recomenda-se que especifique também os seguintes cabeçalhos:

- [Agente-utilizador](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Os cabeçalhos de IP e localização do cliente são importantes para devolver o conteúdo com reconhecimento de local.

Para obter uma lista de todos os cabeçalhos de pedido e resposta, veja [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Quando ligar para a API Bing Autosuggest do JavaScript, as funcionalidades de segurança incorporadas do seu navegador podem impedir que aceda aos valores destes cabeçalhos.

Para resolver isto, pode fazer o pedido de API Bing Autosuggest através de um proxy CORS. A resposta de tal `Access-Control-Expose-Headers` procuração tem um cabeçalho que os cabeçalhos de resposta dos whitelists e os coloca à disposição do JavaScript.

É fácil instalar um proxy CORS para permitir que a nossa [aplicação tutorial](../tutorials/autosuggest.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, introduza o seguinte comando num pedido de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto final bing autosuggest API no ficheiro HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

Os pedidos devem incluir todos os parâmetros de consulta sugeridos e cabeçalhos. 

O seguinte exemplo mostra um pedido que devolve as cadeias de consulta sugeridas para *sail*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o cabeçalho do ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para a combinação de utilizador e dispositivo.

O seguinte grupo de sugestões web é uma resposta ao pedido acima. O grupo contém uma lista de sugestões de `displayText`consulta `query`de `url` pesquisa, com cada sugestão incluindo um , e campo.

O campo `displayText` contém a consulta sugerida que utilizaria para preencher a lista pendente da sua caixa de pesquisa. Tem de apresentar todas as sugestões incluídas na resposta e pela ordem fornecida.  

Se o utilizador selecionar uma consulta da lista de drop-down, pode usá-la para ligar para a das [APIs](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) de Pesquisa bing `url` e exibir os resultados por si mesmo, ou enviar o utilizador para a página de resultados do Bing utilizando o campo devolvido.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Passos seguintes

- [O que é a Sugestão Automática do Bing?](../get-suggested-search-terms.md)
- [Referência da API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Obter termos de pesquisa sugeridos da API Bing Autosuggest](get-suggestions.md)
