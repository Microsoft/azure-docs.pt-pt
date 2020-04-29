---
title: 'Quickstart: Verifique a ortografia com o REST API e PHP - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como uma simples aplicação PHP envia um pedido para a API bing spell check e devolve uma lista de correções sugeridas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 816f2692a71d5d4281248405cc84102cfa881f66
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75382885"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-php"></a>Quickstart: Verifique a ortografia com o Bing Spell Check REST API e PHP

Use este quickstart para fazer a sua primeira chamada para a API de Verificação de Feitiços de Bing. Este simples pedido php envia um pedido à API e devolve uma lista de correções sugeridas. Embora esta aplicação esteja escrita em PHP, a API é um serviço Web restful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [PHP 5.6.x](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="get-spell-check-results"></a>Obter resultados da Verificação de Ortografia

1. Crie um novo projeto PHP no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua subscrição.
4. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.
5. Execute o programa.
    
    ```php
    <?php
    
    // NOTE: Be sure to uncomment the following line in your php.ini file.
    // ;extension=php_openssl.dll
    
    // These properties are used for optional headers (see below).
    // define("CLIENT_ID", "<Client ID from Previous Response Goes Here>");
    // define("CLIENT_IP", "999.999.999.999");
    // define("CLIENT_LOCATION", "+90.0000000000000;long: 00.0000000000000;re:100.000000000000");
    
    $host = 'https://api.cognitive.microsoft.com';
    $path = '/bing/v7.0/spellcheck?';
    $params = 'mkt=en-us&mode=proof';
    
    $input = "Hollo, wrld!";
    
    $data = array (
        'text' => urlencode ($input)
    );
    
    // NOTE: Replace this example key with a valid subscription key.
    $key = 'ENTER KEY HERE';
    
    // The following headers are optional, but it is recommended
    // that they are treated as required. These headers will assist the service
    // with returning more accurate results.
    //'X-Search-Location' => CLIENT_LOCATION
    //'X-MSEdge-ClientID' => CLIENT_ID
    //'X-MSEdge-ClientIP' => CLIENT_IP
    
    $headers = "Content-type: application/x-www-form-urlencoded\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    
    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => http_build_query ($data)
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    
    if ($result === FALSE) {
        /* Handle error */
    }
    
    $json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
    echo $json;
    ?>
    ```


## <a name="run-the-application"></a>Executar a aplicação

Faça a sua aplicação iniciando um servidor web e navegando para o seu ficheiro.

## <a name="example-json-response"></a>Exemplo resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
