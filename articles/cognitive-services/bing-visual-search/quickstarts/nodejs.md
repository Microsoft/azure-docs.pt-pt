---
title: 'Quickstart: Obtenha insights de imagem usando o REST API e Node.js - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379713"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Quickstart: Obtenha insights de imagem usando a API e node.js de pesquisa visual bing

Utilize este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing e veja os resultados da pesquisa. Esta simples aplicação JavaScript envia uma imagem para a API e exibe as informações devolvidas sobre a mesmo. Embora esta aplicação esteja escrita no JavaScript, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* O módulo Request para JavaScript. Pode utilizar `npm install request` o comando para instalar o módulo.
* O módulo de dados de formulários. Pode utilizar `npm install form-data` o comando para instalar o módulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um ficheiro JavaScript no seu IDE ou editor favorito e detete os seguintes requisitos:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Crie variáveis para o seu ponto final de API, chave de subscrição e o caminho para a sua imagem. `baseUri`pode ser o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Criar uma `requestCallback()` função nomeada para imprimir a resposta da API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construir e enviar o pedido de pesquisa

Ao carregar uma imagem local, os `Content-Disposition` dados do formulário devem incluir o cabeçalho. Deve definir `name` o parâmetro para "imagem", e o `filename` parâmetro pode ser definido em qualquer corda. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Crie um novo `FormData()`objeto **FormData** utilizando , e `fs.createReadStream()`apreenda o seu caminho de imagem, utilizando:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Utilize a biblioteca de pedidos para `requestCallback()` fazer upload da imagem e ligue para imprimir a resposta. Certifique-se de adicionar a sua chave de subscrição ao cabeçalho de pedido:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
