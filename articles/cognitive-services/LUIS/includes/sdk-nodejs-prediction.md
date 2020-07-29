---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 19da911cbc662f47e7bbd16aaddf8803d4109d6a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369281"
---
Utilize a biblioteca de clientes de runtime com o Conhecimento de Línguas (LUIS) para Node.js:

* Previsão por slot
* Previsão por Versão

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)  |  [Pacote de tempo de execução (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js)

## <a name="prerequisites"></a>Pré-requisitos

* Recurso de tempo de compreensão da linguagem: [Criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Um ID de aplicativo LUIS - use o ID da aplicação IoT pública de `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A consulta do utilizador utilizada no código quickstart é específica para essa aplicação.

## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha a sua chave de tempo de execução (LUIS)

Obtenha a sua [chave de tempo de execução](../luis-how-to-azure-subscription.md) criando um recurso de tempo de execução LUIS. Guarde a chave e o ponto final da chave para o próximo passo.

### <a name="create-a-new-javascript-nodejs-file"></a>Criar um novo ficheiro JavaScript (Node.js)

Crie um novo ficheiro JavaScript no seu editor preferido ou IDE, denominado `luis_prediction.js` .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instale a biblioteca NPM para o tempo de execução DO LUIS

No diretório de aplicações, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) que autentica a Azure, que contém a sua chave de autoria.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* [Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) por `staging` ou faixa `production` horária
* [Previsão por versão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de previsão de idioma (LUIS) de previsão:

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o `luis_prediction.js` arquivo no seu editor preferido ou IDE. Adicione as seguintes dependências:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a sua própria informação LUIS requerida:

    Adicione variáveis para gerir a sua chave de previsão e chave de ponto final. 
    
    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Crie uma variável para o ID da aplicação chamado `LUIS_APP_ID` . Desaprote a variável para a aplicação IoT pública, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir a `production` ranhura publicada.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Crie um objeto msRest.ApiKeyCredentials com a sua chave e use-o com o seu ponto final para criar um [LUIS. Objeto LUISRuntimeClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte método para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [predictionRequest.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)

O método **[luisRuntimeClient.forecast.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** necessita de vários parâmetros, tais como o ID da aplicação, o nome da slot e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Utilize o seguinte método principal para ligar as variáveis e métodos para obter a previsão.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node luis_prediction.js` comando do seu diretório de candidaturas.

```console
node luis_prediction.js
```

O resultado da previsão devolve um objeto JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, eliminando o ficheiro e as suas subdiretas.
