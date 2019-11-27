---
title: 'Início rápido: obter informações de imagem usando a API REST e node. js-Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem no API da Pesquisa Visual do Bing e obter informações sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: ecfe341fa050e693f919f35c29c8120c687c88f8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383198"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Início rápido: obter informações de imagem usando a API REST do Pesquisa Visual do Bing e o Node. js

Use este guia de início rápido para fazer sua primeira chamada para a API da Pesquisa Visual do Bing e exibir os resultados da pesquisa. Esse aplicativo JavaScript simples carrega uma imagem na API e exibe as informações retornadas sobre ela. Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Ao carregar uma imagem local, os dados do formulário devem incluir o cabeçalho `Content-Disposition`. Você deve definir seu parâmetro `name` como "Image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* O módulo de solicitação para JavaScript. Você pode usar `npm install request` comando para instalar o módulo.
* O módulo de dados de formulário. Você pode usar o comando `npm install form-data` para instalar o módulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um arquivo JavaScript em seu IDE ou editor favorito e defina os seguintes requisitos:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Crie variáveis para seu ponto de extremidade de API, chave de assinatura e o caminho para a imagem:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Crie uma função chamada `requestCallback()` para imprimir a resposta da API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construir e enviar a solicitação de pesquisa

1. Crie um novo objeto **FormData** usando `FormData()`e anexe o caminho da imagem a ele, usando `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Use a biblioteca de solicitações para carregar a imagem e chame `requestCallback()` para imprimir a resposta. Certifique-se de adicionar sua chave de assinatura ao cabeçalho de solicitação:

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
> [Criar um Pesquisa Visual aplicativo Web de página única](../tutorial-bing-visual-search-single-page-app.md)
