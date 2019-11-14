---
title: Enviando solicitações para o API de Sugestão Automática do Bing
titleSuffix: Azure Cognitive Services
description: O API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de caracteres de consulta parcial na caixa de pesquisa. Saiba mais sobre como enviar solicitações.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072853"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Envio de solicitações para o API de Sugestão Automática do Bing.

Se seu aplicativo enviar consultas para qualquer um dos APIs de Pesquisa do Bing, você poderá usar o API de Sugestão Automática do Bing para melhorar a experiência de pesquisa de seus usuários. O API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de caracteres de consulta parcial na caixa de pesquisa. À medida que os caracteres são inseridos em uma caixa de pesquisa em seu aplicativo, você pode exibir sugestões em uma lista suspensa. Use este artigo para saber mais sobre como enviar solicitações para essa API. 

## <a name="bing-autosuggest-api-endpoint"></a>Ponto de extremidade API de Sugestão Automática do Bing

O **API de sugestão automática do Bing** inclui um ponto de extremidade, que retorna uma lista de consultas sugeridas de um termo de pesquisa parcial.

Para obter consultas sugeridas usando a API do Bing, envie uma solicitação de `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir outras especificações.

**Ponto de extremidade:** Retorna sugestões de pesquisa como resultados JSON que são relevantes para a entrada do usuário definida por `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência do [API de sugestão automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) .

As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.
Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de sugestão automática, consulte [autosugestão de início rápido](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Solicitações de API de Sugestão Automática do Bing

> [!NOTE]
> * As solicitações para o API de Sugestão Automática do Bing devem usar o protocolo HTTPS.

Recomendamos que todos os pedidos tenham origem num servidor. Distribuir a chave como parte de um aplicativo cliente fornece acesso de terceiros mal-intencionado a mais oportunidades. Além disso, fazer chamadas de um servidor fornece um único ponto de atualização para atualizações futuras.

O pedido tem de especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), que contém o termo de pesquisa parcial do utilizador. Embora seja opcional, o pedido deve também especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), que identifica o mercado de onde pretende que os resultados provenham. Para obter uma lista opcional de parâmetros de consulta, veja [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta têm de estar codificados com URL.

O pedido tem de especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Embora seja opcional, recomenda-se que especifique também os seguintes cabeçalhos:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Os cabeçalhos de IP e localização do cliente são importantes para devolver o conteúdo com reconhecimento de local.

Para obter uma lista de todos os cabeçalhos de pedido e resposta, veja [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Quando você chama o API de Sugestão Automática do Bing do JavaScript, os recursos de segurança internos do seu navegador podem impedi-lo de acessar os valores desses cabeçalhos.

Para resolver isso, você pode fazer a solicitação de API de Sugestão Automática do Bing por meio de um proxy CORS. A resposta de tal proxy tem um cabeçalho `Access-Control-Expose-Headers` que lista as brancas cabeçalhos de resposta e os disponibiliza para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo tutorial](../tutorials/autosuggest.md) acesse os cabeçalhos de cliente opcionais. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, digite o comando a seguir em um prompt de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade API de Sugestão Automática do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

As solicitações devem incluir todos os parâmetros e cabeçalhos de consulta sugeridos. 

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

O seguinte grupo de sugestões da Web é uma resposta à solicitação acima. O grupo contém uma lista de sugestões de consulta de pesquisa, com cada sugestão, incluindo um campo `displayText`, `query`e `url`.

O campo `displayText` contém a consulta sugerida que utilizaria para preencher a lista pendente da sua caixa de pesquisa. Tem de apresentar todas as sugestões incluídas na resposta e pela ordem fornecida.  

Se o usuário selecionar uma consulta na lista suspensa, você poderá usá-la para chamar uma das [APIs de pesquisa do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) e exibir os resultados por conta própria ou enviar o usuário para a página de resultados do Bing usando o campo de `url` retornado.

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
- [Obtendo os termos de pesquisa sugeridos do API de Sugestão Automática do Bing](get-suggestions.md)
