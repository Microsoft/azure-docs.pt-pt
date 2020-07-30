---
title: 'Quickstart: Obtenha insights de imagem utilizando a API REST e Node.js - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual Bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-javascript
ms.openlocfilehash: 1e5594fab6e4b1758e8e3cd722c053b9d3d301ce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404027"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Quickstart: Obtenha insights de imagem utilizando a API e o Node.js de pesquisa visual Bing Visual

Utilize este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing. Esta simples aplicação JavaScript envia uma imagem para a API e exibe as informações devolvidas sobre a presente. Embora esta aplicação esteja escrita em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* O módulo pedido para JavaScript. Pode utilizar `npm install request` o comando para instalar o módulo.
* O módulo de dados do formulário. Pode utilizar o `npm install form-data` comando para instalar o módulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um ficheiro JavaScript no seu IDE ou editor favorito e desaprove os seguintes requisitos:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Crie variáveis para o seu ponto final da API, chave de subscrição e o caminho para a sua imagem. Pelo `baseUri` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Criar uma função nomeada `requestCallback()` para imprimir a resposta da API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construa e envie o pedido de pesquisa

1. Ao carregar uma imagem local, os dados do formulário devem incluir o `Content-Disposition` cabeçalho. Desaveie o seu `name` parâmetro para "imagem", e desave o `filename` parâmetro para o nome do ficheiro da sua imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Crie um novo `FormData` objeto com , e `FormData()` apromte o seu caminho de imagem para ele usando `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Use a biblioteca de pedidos para fazer o upload da imagem e ligue `requestCallback()` para imprimir a resposta. Adicione a chave de subscrição ao cabeçalho de pedido.

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

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
