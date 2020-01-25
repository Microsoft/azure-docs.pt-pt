---
title: Obter modelo com chamada REST emC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a262db04e51015edb760a8b04952dfa24b2ad63a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748781"
---
## <a name="prerequisites"></a>Pré-requisitos

* Chave inicial.
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório do GitHub cognitiva-Services-Language-Understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. O ID da aplicação é apresentado no dashboard de aplicações.
* A ID da versão no aplicativo que recebe o declarações. O ID predefinido é "0.1".
* Linguagem de programação [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programaticamente

Use o Go para adicionar uma [API](https://aka.ms/luis-apim-v3-authoring) de entidade aprendida por máquina ao aplicativo. 

1. Crie um novo ficheiro com o nome `model.js`. Adicione o seguinte código:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Substitua os seguintes valores:

    * `YOUR-KEY` com sua chave inicial
    * `YOUR-ENDPOINT` com seu ponto de extremidade, por exemplo, `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` com a ID do seu aplicativo

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, digite o seguinte comando para executar o arquivo:

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)