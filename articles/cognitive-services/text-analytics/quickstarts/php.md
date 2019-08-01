---
title: 'Início rápido: Usando PHP para chamar o API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar rapidamente a usar o API de Análise de Texto nos serviços cognitivas do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 62dd04b6df465b82bb1b39e23afce410325c4248
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697328"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Início rápido: Usando PHP para chamar o serviço de Análise de Texto cognitiva
<a name="HOLTop"></a>

Este artigo mostra como detectar o [idioma](#Detect), [analisar sentimentos](#SentimentAnalysis), [extrair frases-chave](#KeyPhraseExtraction)e [identificar entidades vinculadas](#Entities) usando as [APIs](//go.microsoft.com/fwlink/?LinkID=759711) de análise de texto com php.

Veja as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para ter acesso à documentação técnica sobre APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Detetar idioma

A API Deteção de Idioma deteta o idioma de um documento de texto através do [método Detetar Idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Crie um novo projeto PHP no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `accessKey` por uma chave de acesso válida para a sua subscrição.
4. Substitua a localização em `host` (atualmente `westus`) pela região em que se inscreveu.
5. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.1/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta de deteção de idioma**

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analisar sentimento

A API da Análise de Sentimentos deteta o sentimento de um conjunto de registos de texto através do [método Sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). O seguinte exemplo classifica dois documentos, um em inglês e outro em espanhol.

1. Crie um novo projeto PHP no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `accessKey` por uma chave de acesso válida para a sua subscrição.
4. Substitua a localização em `host` (atualmente `westus`) pela região em que se inscreveu.
5. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.1/sentiment';

function GetSentiment ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Resposta de análise de sentimentos**

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrair frases-chave

A API de Extração de Expressões-Chave extrai expressões-chave de um documento de texto através do [método Expressões-Chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). O seguinte exemplo extrai expressões-chave para o documento em inglês e o documento em espanhol.

1. Crie um novo projeto PHP no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `accessKey` por uma chave de acesso válida para a sua subscrição.
4. Substitua a localização em `host` (atualmente `westus`) pela região em que se inscreveu.
5. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.1/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>

```

**Resposta de extração de expressões-chave**

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificar as entidades 

A API de Entidades identifica entidades conhecidas num documento de texto através do [método Entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). O seguinte exemplo identifica as entidades dos documentos em inglês.

1. Crie um novo projeto PHP no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `accessKey` por uma chave de acesso válida para a sua subscrição.
4. Substitua a localização em `host` (atualmente `westus`) pela região em que se inscreveu.
5. Execute o programa.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
$accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
$host = 'https://westus.api.cognitive.microsoft.com';
$path = '/text/analytics/v2.1/entities';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities ($host, $path, $accessKey, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>

```

**Resposta de extração de entidades**

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
