---
title: 'Início rápido: Analisar uma imagem remota - REST, node. js'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, vai analisar uma imagem através da API de Imagem Digitalizada com o Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8ba09d1841ada25f6b763f187f629ef0c7eae598
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548035"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-with-nodejs-in-computer-vision"></a>Início rápido: Analisar uma imagem remota através da API REST com node. js na visão do computador

Neste guia de início rápido, vai analisar uma imagem armazenada remotamente para extrair caraterísticas visuais com a API REST de Imagem Digitalizada. Com o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analisar Imagem), pode extrair caraterísticas visuais com base no conteúdo da imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Node.js](https://nodejs.org) 4.x ou posterior instalado.
- Tem de ter o [npm](https://www.npmjs.com/) instalado.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Instale o pacote [`request`](https://www.npmjs.com/package/request) do npm.
   1. Abra a janela da linha de comandos como administrador.
   1. Execute o seguinte comando:

      ```console
      npm install request
      ```

   1. Depois de o pacote ser instalado com êxito, feche a janela da linha de comandos.

1. Copie o código seguinte para um editor de texto.
1. Faça as alterações seguintes ao código, onde for necessário:
    1. Substitua o valor de `subscriptionKey` pela chave de subscrição.
    1. Substitua o valor de `uriBase` pelo URL de ponto final do método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analisar Imagem) da região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, substitua o valor de `imageUrl` pelo URL de uma imagem diferente que pretende analisar.
    1. Opcionalmente, substitua o valor do parâmetro de pedido `language` por um idioma diferente.
1. Guarde o código como um ficheiro com uma extensão `.js`. Por exemplo, `analyze-image.js`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `node` para executar o ficheiro. Por exemplo, `node analyze-image.js`.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. O exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o ficheiro e, em seguida, desinstale o pacote `request` do npm. Para desinstalar o pacote, siga os seguintes passos:

1. Abra a janela da linha de comandos como administrador.
2. Execute o seguinte comando:

   ```console
   npm uninstall request
   ```

3. Depois de o pacote ser desinstalado com êxito, feche a janela da linha de comandos.

## <a name="next-steps"></a>Passos Seguintes

Explore as API de Imagem Digitalizada utilizadas para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
