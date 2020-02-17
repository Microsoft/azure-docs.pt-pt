---
title: Obter modelo com chamada REST emC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368460"
---
## <a name="prerequisites"></a>Pré-requisitos

* Compreensão da língua azure - chave de caracteres de recursos 32 e url de ponto final de autor. Criar com o [portal Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importar a aplicação [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório gitHub compreensivo cognitivo-serviços.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. O ID da aplicação é apresentado no dashboard de aplicações.
* A ID da versão no aplicativo que recebe o declarações. O ID predefinido é "0.1".
* Linguagem de programação [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Alterar modelo programaticamente

1. Crie um novo ficheiro com o nome `model.js`. Adicione o seguinte código:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
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

1. Substitua os valores a partir de `YOUR-` pelos seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-KEY`|A tua chave de autor de 32 personagens.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de autoria. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Definiu o seu nome de recurso quando criou o recurso.|
    |`YOUR-APP-ID`| O seu ID de aplicativo LUIS. |

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos do Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página definições de **aplicações.**

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, digite o seguinte comando para executar o arquivo:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Boas práticas para uma aplicação](../luis-concept-best-practices.md)