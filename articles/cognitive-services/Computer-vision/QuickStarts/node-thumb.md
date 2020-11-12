---
title: 'Início Rápido: Gerar uma miniatura – REST, Node.js'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura a partir de uma imagem através da API de Imagem Digitalizada com o Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: d4ccf54d5aa95889a425dc4b51afb509cf003a71
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535916"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Quickstart: Gere uma miniatura utilizando a API e o Node.js

Neste arranque rápido, gerará uma miniatura a partir de uma imagem utilizando a API de Visão de Computador. Com o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c), pode gerar uma miniatura de uma imagem. Especifica a altura e a largura, que podem ser diferentes da proporção da imagem de entrada. A Computer Vision utiliza a cultura inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de corte com base nessa região.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4.x ou mais tarde 
* [npm](https://www.npmjs.com/) 
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [Crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` e, respectivamente.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Instale o [`request`](https://www.npmjs.com/package/request) pacote npm.
   1. Abra a janela da linha de comandos como administrador.
   1. Execute o seguinte comando:

      ```console
      npm install request
      ```

   1. Depois de o pacote ser instalado com êxito, feche a janela da linha de comandos.

1. Copie o código seguinte para um editor de texto.
1. Opcionalmente, substitua o valor de `imageUrl` pelo URL de uma imagem diferente que pretende analisar.
1. Guarde o código como um ficheiro com uma extensão `.js`. Por exemplo, `get-thumbnail.js`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `node` para executar o ficheiro. Por exemplo, `node get-thumbnail.js`.

```javascript
'use strict';

const fs = require('fs');
const request = require('request').defaults({ encoding: null });

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']

var uriBase = endpoint + 'vision/v3.1/generateThumbnail';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

// Construct the request
const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
}

// Post the request and get the response (an image stream)
request.post(options, (error, response, body) => {
    // Write the stream to file
    var buf = Buffer.from(body, 'base64');
    fs.writeFile('thumbnail.png', buf, function (err) {
        if (err) throw err;
    });

    console.log('Image saved')
});
```

## <a name="examine-the-response"></a>Examinar a resposta

Um pop-up da imagem da miniatura será exibido.
É devolvida uma resposta com êxito como dados binários, que representa os dados da imagem da miniatura. Se o pedido falhar, a resposta é apresentada na janela da consola. A resposta do pedido falhado contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

## <a name="next-steps"></a>Passos seguintes

Em seguida, explore a API de Visão Computacional usada para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito.

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d)
