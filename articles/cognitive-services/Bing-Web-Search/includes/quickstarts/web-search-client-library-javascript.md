---
title: Bing Web Search JavaScript biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 0a505e5fb11e776a90c8e198b2568e2ac0bd1854
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406384"
---
A biblioteca de clientes Bing Web Search facilita a integração da Bing Web Search na sua aplicação Node.js. Neste início rápido, irá aprender a instanciar um cliente, a enviar um pedido e a imprimir a resposta.

Quer ver o código imediatamente? As amostras para as bibliotecas de [clientes Bing Search para JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) estão disponíveis no GitHub.

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão algumas coisas de que irá precisar antes de executar este início rápido:

* [Node.js 6](https://nodejs.org/en/download/) ou posterior
* Uma chave de subscrição  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Vamos começar por configurar o ambiente de desenvolvimento para o nosso projeto do Node.js.

1. Crie um diretório novo para o seu projeto:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Crie um novo ficheiro de pacote:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Agora, vamos instalar alguns módulos do Azure e adicioná-los a `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Criar um projeto e declarar os módulos exigidos

No mesmo diretório que o seu `package.json`, crie um novo projeto do Node.js com o seu IDE ou editor favorito. Por exemplo: `sample.js`.

Em seguida, copie este código para o seu projeto. Ele carrega os módulos instalados na secção anterior.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Instanciar o cliente

Este código instancia um cliente e utiliza o módulo `azure-cognitiveservices-websearch`. Certifique-se de que introduz uma chave de subscrição válida para a sua conta do Azure antes de continuar.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Fazer um pedido e imprimir os resultados

Utilize o cliente para enviar uma consulta de pesquisa para a Pesquisa na Web do Bing. Se a resposta inclui os resultados para qualquer um dos itens na matriz `properties`, o `result.value` é impresso na consola.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Executar o programa

O passo final é executar o programa!

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado de fazer o que quer neste projeto, não se esqueça de remover a sua chave de subscrição do código do programa.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Serviços Cognitivos Node.js amostras de SDK](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Ver também

* [Referência do SDK do Node do Azure](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
