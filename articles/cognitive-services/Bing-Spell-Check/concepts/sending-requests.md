---
title: Enviar pedidos para a API de Verificação de Ortografia do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os modos Bing Spell Check, definições e outras informações relacionadas com a API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 761cc3908b677b129e85be442b7a593a38a367f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341568"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Enviar pedidos para a API de Verificação de Ortografia do Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Para verificar se existem erros de ortografia e de gramática numa cadeia de texto, enviaria um pedido GET para o seguinte ponto final:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
O pedido tem de utilizar o protocolo HTTPS.

Recomendamos que todos os pedidos tenham origem num servidor. Distribuir a chave como parte de uma aplicação cliente fornece mais oportunidades para terceiros mal-intencionados acederem à mesma. Um servidor também fornece um único ponto de atualização para futuras versões da API.

O pedido tem de especificar o parâmetro de consulta [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text), que contém a cadeia de texto a verificar. Embora seja opcional, o pedido deve também especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt), que identifica o mercado de onde pretende que os resultados provenham. Para obter uma lista opcional de parâmetros de consulta, como `mode`, veja [Parâmetros de Consulta](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters). Todos os valores de parâmetro de consulta têm de estar codificados com URL.  
  
O pedido tem de especificar o cabeçalho [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). Embora opcional, é encorajado a especificar também os seguintes cabeçalhos. Estes cabeçalhos ajudam a Bing Spell Check API a devolver resultados mais precisos:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Para obter uma lista de todos os cabeçalhos de pedido e resposta, veja [Cabeçalhos](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Ao ligar para a API de Verificação ortográfica Bing usando o JavaScript, as funcionalidades de segurança incorporadas do seu navegador podem impedir-lhe de aceder aos valores destes cabeçalhos.

Para resolver este problema, pode efetuar o pedido de API de Verificação ortográfica de Bing através de um representante do CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra cabeçalhos de resposta e os coloca disponíveis para JavaScript.

É fácil instalar um proxy CORS para permitir que a [aplicação tutorial](../tutorials/spellcheck.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, insira o seguinte comando num pedido de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto final de verificação de feitiço de Bing no ficheiro HTML para:\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

Por fim, inicie o proxy do CORS com o comando seguinte:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção http headers expansível abaixo dos resultados da pesquisa, pode agora ver o `X-MSEdge-ClientID` cabeçalho (entre outros) e verificar se é o mesmo para cada pedido.

## <a name="example-api-request"></a>Exemplo pedido de API

A seguir, é-lhe mostrado um pedido que inclui todos os parâmetros de consulta e cabeçalhos sugeridos. Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para o utilizador e a combinação de dispositivo. 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

O código a seguir mostra a resposta ao pedido anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Passos seguintes

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)