---
title: 'Início rápido: Enviar consultas de pesquisa com a API de pesquisa de imagens do Bing e o PHP'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para procurar e localizar imagens na web usando a API de pesquisa Web Bing.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/07/2018
ms.author: aahi
ms.openlocfilehash: ebf8c0269e070c9047d730e58b8e2d3824124e1a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574203"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-php"></a>Início rápido: Consultas de pesquisa de envio utilizar a API de REST de pesquisa de imagens do Bing e o PHP

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de imagens do Bing e receber uma resposta JSON. O aplicativo simple neste artigo envia uma consulta de pesquisa e exibe os resultados brutos.

Embora esse aplicativo é escrito em PHP, a API é um serviço RESTful Web compatível com qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON.

O código-fonte para este exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingImageSearchv7.java).

## <a name="prerequisites"></a>Pré-requisitos

* [PHP 5.6.x ou posterior](http://php.net/downloads.php).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

Para executar esta aplicação, siga estes passos.

1. Certifique-se de que o suporte para HTTP seguro está ativado no seus `php.ini` ficheiro. No Windows, este ficheiro está localizado no `C:\windows`.
2. Crie um novo projeto PHP no seu IDE ou editor preferido.
3. definir o ponto de final de API, a chave de subscrição e o termo de pesquisa.

    ```php
    $endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';
    // Replace the accessKey string value with your valid access key.
    $accessKey = 'enter key here';
    $term = 'tropical ocean';
    ```
## <a name="construct-and-perform-a-http-request"></a>Criar e executar um pedido HTTP

1. Utilize as variáveis do último passo para preparar um pedido HTTP para a API de pesquisa de imagens.

    ```php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                            'header' => $headers,
                            'method' => 'GET' ));
    ```
2. Executar a solicitação da web e obtenha a resposta JSON.

    ```php
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);
    ```

## <a name="process-and-print-the-json"></a>Processar e imprimir o JSON

Processar e a resposta JSON devolvida de impressão.

    ```php
    $headers = array();
        foreach ($http_response_header as $k => $v) {
            $h = explode(":", $v, 2);
            if (isset($h[1]))
                if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                    $headers[trim($h[0])] = trim($h[1]);
        }
        return array($headers, $result);
    ```

## <a name="sample-json-response"></a>Resposta JSON de exemplo

Respostas a partir da API de pesquisa de imagens do Bing são retornadas como JSON. Esta resposta de amostra foi truncada para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial de aplicação de página única de pesquisa de imagens Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a pesquisa de imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Tente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obter uma chave de acesso de serviços cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentação dos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de pesquisa de imagens do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)