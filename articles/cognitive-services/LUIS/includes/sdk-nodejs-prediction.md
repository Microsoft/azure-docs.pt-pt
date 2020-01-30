---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 0539f4a8080056f96319a7a75a355782ee80018d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772429"
---
Utilize a biblioteca de clientes em tempo de execução (LUIS) para:

* Previsão por ranhura
* Previsão por Versão

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [código fonte da Biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | Pacote de Prazo de [Execução (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Pré-requisitos

* Recurso de tempo de execução da compreensão linguística: [Criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha a sua chave de tempo de execução da linguagem (LUIS)

Obtenha a sua chave de tempo de [execução](../luis-how-to-azure-subscription.md) criando um recurso de tempo de execução LUIS. Mantenha sua chave e o ponto de extremidade da chave para a próxima etapa.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Crie um novo ficheiro javascript (Node.js)

Crie um novo ficheiro javascript no seu editor preferido ou IDE, chamado `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instale a biblioteca NPM para o tempo de execução do LUIS

No diretório do aplicativo, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de criação de Reconhecimento vocal (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) que se autentica no Azure, que contém a sua chave de criação.

Depois que o cliente for criado, use esse cliente para acessar a funcionalidade, incluindo:

* [Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) por `staging` ou `production` slot
* [Previsão por versão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes runtime de previsão de previsão de linguagem (LUIS):

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicionar as dependências

A partir do diretório do projeto, abra o ficheiro `luis_prediction.js` no seu editor ou IDE preferido. Adicione as seguintes dependências:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a sua própria informação necessária do LUIS:

    Adicione variáveis para gerir a sua chave de previsão retirada de uma variável ambiental chamada `LUIS_RUNTIME_KEY`. Se você criou a variável de ambiente depois que o aplicativo é iniciado, o editor, IDE ou Shell em execução precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente.

    Crie uma variável para manter o seu nome de recurso `LUIS_RUNTIME_ENDPOINT`.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Crie uma variável para o ID da aplicação como uma variável ambiental chamada `LUIS_APP_ID`. Desloque a variável ambiental para a aplicação ioT pública, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir a ranhura publicada `production`.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Crie um objeto msRest.ApiKeyCredentials com a sua chave e use-o com o seu ponto final para criar um [LUIS. Objeto DE CLIENTE LUISRuntime.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obter previsão do tempo de execução

Adicione o método a seguir para criar a solicitação para o tempo de execução de previsão.

A expressão do utilizador faz parte do objeto [de previsãoRequest.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)

O método **[luisRuntimeClient.forecast.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** necessita de vários parâmetros, tais como o ID da aplicação, o nome da ranhura e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como Verbose, mostram todas as intenções e log são opcionais.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Use o método Main a seguir para vincular as variáveis e os métodos para obter a previsão.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando `node luis_prediction.js` do seu diretório de aplicações.

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


## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, apagando o ficheiro e os seus subdiretórios.
