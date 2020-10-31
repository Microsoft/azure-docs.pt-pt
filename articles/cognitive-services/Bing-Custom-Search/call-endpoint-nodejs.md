---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Node.js / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar os resultados da pesquisa a partir da sua instância de Pesquisa Personalizada Bing utilizando Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b0fe87b199be81d5f79a8e9530c927e391e4ddf7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101770"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Node.js

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em JavaScript, a API de Pesquisa Personalizada Bing é um serviço web RESTful compatível com a maioria das linguagens de programação. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Para obter mais informações, consulte [Quickstart: Crie a sua primeira instância de Pesquisa Personalizada Bing](quick-start.md).

- [O Node.js tempo de execução do JavaScript](https://www.nodejs.org/).

- A [biblioteca de pedidos JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

- Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione uma `require()` declaração para a biblioteca de pedidos. Crie variáveis para a sua chave de subscrição, ID de configuração personalizada e termo de pesquisa.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie uma variável para armazenar a informação enviada no seu pedido. Construa o URL de pedido, anexando o seu termo de pesquisa ao `q=` parâmetro de consulta, e o ID de configuração personalizado da sua instância de pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Utilize a biblioteca de pedidos JavaScript para enviar um pedido de pesquisa para a sua instância de Pesquisa Personalizada Bing e imprima informações sobre os resultados, incluindo o seu nome, url e a data em que a página foi rastreada pela última vez.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa um aplicativo web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
