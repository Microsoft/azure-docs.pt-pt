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
ms.date: 05/22/2020
ms.author: scottwhi
ms.openlocfilehash: 7dfb3adb5d7bf5b005beb7e7b75fb339d456cd15
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872607"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Quickstart: Obtenha insights de imagem usando a API e node.js de pesquisa visual bing

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing. Esta simples aplicação JavaScript envia uma imagem para a API e exibe as informações devolvidas sobre a mesmo. Embora esta aplicação esteja escrita no JavaScript, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* O módulo Request para JavaScript. Pode utilizar `npm install request` o comando para instalar o módulo.
* O módulo de dados de formulários. Pode utilizar o `npm install form-data` comando para instalar o módulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um ficheiro JavaScript no seu IDE ou editor favorito e detete os seguintes requisitos:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Crie variáveis para o seu ponto final de API, chave de subscrição e o caminho para a sua imagem. Para o `baseUri` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Crie uma função nomeada `requestCallback()` para imprimir a resposta da API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construir e enviar o pedido de pesquisa

1. Ao fazer o upload de uma imagem local, os dados do formulário devem incluir o `Content-Disposition` cabeçalho. Defina o parâmetro `name` para "imagem", e coloque o `filename` parâmetro no nome de ficheiro da sua imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Crie um novo `FormData` objeto com , e `FormData()` apreenda o seu caminho de imagem utilizando `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Utilize a biblioteca de pedidos para fazer upload da imagem e ligue `requestCallback()` para imprimir a resposta. Adicione a sua chave de subscrição ao cabeçalho de pedido.

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
