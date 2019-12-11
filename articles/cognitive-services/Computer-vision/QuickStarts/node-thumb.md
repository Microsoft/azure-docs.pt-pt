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
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ac97ae1d3eb260a9e0c70d3f831efb8b56a5c29d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973820"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Início rápido: gerar uma miniatura usando a API REST do Pesquisa Visual Computacional e o Node. js

Neste guia de início rápido, você gerará uma miniatura de uma imagem usando a API REST do Pesquisa Visual Computacional. Com o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), pode gerar uma miniatura de uma imagem. Especifica a altura e a largura, que podem ser diferentes da proporção da imagem de entrada. Pesquisa Visual Computacional usa o corte inteligente para identificar de forma inteligente a área de interesse e gerar coordenadas de corte com base nessa região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Node.js](https://nodejs.org) 4.x ou posterior instalado.
- Tem de ter o [npm](https://www.npmjs.com/) instalado.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de avaliação gratuita de [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional e obter sua chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a cadeia de caracteres de ponto de extremidade de serviço e chave, denominada `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

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
1. Opcionalmente, substitua o valor de `imageUrl` pelo URL de uma imagem diferente que pretende analisar.
1. Guarde o código como um ficheiro com uma extensão `.js`. Por exemplo, `get-thumbnail.js`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `node` para executar o ficheiro. Por exemplo, `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
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
});
```

## <a name="examine-the-response"></a>Examinar a resposta

É devolvida uma resposta com êxito como dados binários, que representa os dados da imagem da miniatura. Se o pedido falhar, a resposta é apresentada na janela da consola. A resposta do pedido falhado contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

## <a name="next-steps"></a>Passos seguintes

Explore a API de Imagem Digitalizada utilizada para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
