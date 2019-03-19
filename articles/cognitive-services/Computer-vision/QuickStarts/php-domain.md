---
title: 'Início rápido: Conteúdo da imagem de específicas de domínio - REST, PHP'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, vai utilizar um modelo de domínio para identificar marcos numa imagem através da API de Imagem Digitalizada com o PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2c1801296247ace081a34a137fe3f3bea7a55974
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877025"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Início rápido: Reconhecer conteúdo específico do domínio com a REST API e PHP de imagem digitalizada

Neste guia de início rápido, vai utilizar um modelo de domínio para identificar marcos ou, opcionalmente, celebridades numa imagem armazenada remotamente através da API REST de Imagem Digitalizada. Com o método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Reconhecer Conteúdo Específico do Domínio), pode aplicar um modelo específico do domínio para reconhecer conteúdo dentro de uma imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [PHP](https://secure.php.net/downloads.php) instalado.
- Tem de ter o [Pear](https://pear.php.net) instalado.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Instale o pacote [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) do PHP5.
   1. Abra a janela da linha de comandos como administrador.
   1. Execute o seguinte comando:

      ```console
      pear install HTTP_Request2
      ```

   1. Depois de o pacote ser instalado com êxito, feche a janela da linha de comandos.

1. Copie o código seguinte para um editor de texto.
1. Faça as alterações seguintes ao código, onde for necessário:
    1. Substitua o valor de `subscriptionKey` pela chave de subscrição.
    1. Substitua o valor de `uriBase` pelo URL de ponto final do método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Reconhecer Conteúdo Específico do Domínio) da região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, substitua o valor de `imageUrl` pelo URL de uma imagem diferente que pretende analisar.
    1. Opcionalmente, substitua o valor do parâmetro do pedido `domain` por `celebrites` se quiser utilizar o modelo de domínio `celebrities` em vez do modelo de domínio `landmarks`.
1. Guarde o código como um ficheiro com uma extensão `.php`. Por exemplo, `use-domain-model.php`.
1. Abra uma janela do browser com suporte do PHP.
1. Arraste e largue o ficheiro na janela do browser.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. O site de exemplo analisa e apresenta uma resposta de êxito na janela do browser, semelhante ao seguinte exemplo:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o ficheiro e, em seguida, desinstale o pacote `HTTP_Request2` do PHP5. Para desinstalar o pacote, siga os seguintes passos:

1. Abra a janela da linha de comandos como administrador.
2. Execute o seguinte comando:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Depois de o pacote ser desinstalado com êxito, feche a janela da linha de comandos.

## <a name="next-steps"></a>Passos Seguintes

Explore a API de Imagem Digitalizada utilizada para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
