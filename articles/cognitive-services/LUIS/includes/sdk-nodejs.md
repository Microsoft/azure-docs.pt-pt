---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: 0c3a02461d8ad4bc8470383244dad7b86b47f405
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303694"
---
Utilize as bibliotecas de clientes de Compreensão linguística (LUIS) para Node.js:

* Criar uma aplicação
* Adicione uma intenção, uma entidade aprendida com máquinas, com um exemplo de expressão
* Treinar e publicar app
* Runtime de predição da consulta

[Documentação de referência](/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)  |   [Código fonte da](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) Biblioteca de [Autoria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) e Previsão [Autoria](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) e [Previsão](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM / [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org)
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a sua subscrição Azure, [crie um recurso de autoria de Compreensão de Idiomas](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que [criar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) para ligar a sua aplicação à autoria de Compreensão linguística. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido. Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-javascript-application"></a>Criar uma nova aplicação JavaScript

1. Numa janela de consola, crie um novo diretório para a sua aplicação e mude-se para esse diretório.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Inicialize o diretório como uma aplicação JavaScript criando um `package.json` ficheiro.

    ```console
    npm init -y
    ```

1. Crie um ficheiro nomeado `index.js` para o seu código JavaScript.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Instalar as bibliotecas NPM

No diretório de aplicações, instale as dependências com os seguintes comandos, executadas uma linha de cada vez:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

Devia `package.json` parecer:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Modelo de objeto de autoria

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) que autentica a Azure, que contém a sua chave de autoria.

## <a name="code-examples-for-authoring"></a>Exemplos de código para autoria

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [adicionar,](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-) [excluir,](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) [publicar](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemplo de declarações - [adicionar por lote](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), excluir por [ID](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Features - gerir [listas de frases](/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modelo - gerir [intenções](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) e entidades
* Pattern - gerir [padrões](/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Train - [treine](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) a app e a sondagem para [o estado da formação](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versões](/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - gerir com clone, exportar e eliminar

## <a name="prediction-object-model"></a>Modelo de objeto de previsão

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) que autentica a Azure, que contém a sua chave de autoria.

## <a name="code-examples-for-prediction-runtime"></a>Exemplos de código para o tempo de execução da previsão

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* [Previsão](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) por `staging` ou faixa `production` horária
* [Previsão por versão](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Adicione as dependências

Abra o `index.js` ficheiro no seu editor preferido ou IDE nomeado em seguida adicione as seguintes dependências.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Adicione o código da placa de caldeira

1. Adicione o `quickstart` método e a sua chamada. Este método contém a maior parte do código restante. Este método é chamado no final do ficheiro.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Adicione o código restante no método de arranque rápido, salvo especificação em contrário.

## <a name="create-variables-for-the-app"></a>Criar variáveis para a app

Crie dois conjuntos de variáveis: o primeiro conjunto que alterar, o segundo conjunto sai à medida que aparecem na amostra de código. 

1. Crie variáveis para manter os nomes das chaves de autoria e dos recursos.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Crie variáveis para manter os seus pontos finais, nome de aplicação, versão e nome de intenção.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

Uma aplicação LUIS contém o modelo de processamento de linguagem natural (NLP), incluindo intenções, entidades e palavras de exemplo.

Crie um método de [adicionar](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) de um objeto [appsoperação](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) para criar a aplicação. O nome e a cultura linguística são propriedades necessárias.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma aplicação LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_ de expressão de utilizador. Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _determinada de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo numa cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Use o método [model.add_intent](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) com o nome da intenção única e depois passe o ID da aplicação, o ID da versão e o novo nome de intenção.

O `intentName` valor é codificado em código rígido como `OrderPizzaIntent` parte das variáveis na secção Criar variáveis para a secção [de aplicações.](#create-variables-for-the-app)

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informações da expressão do utilizador, necessárias para preencher a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  As entidades pré-construídas comuns para adicionar à sua app incluem [número,](../luis-reference-prebuilt-number.md) [datatimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografiaV2,](../luis-reference-prebuilt-geographyv2.md) [ordinal.](../luis-reference-prebuilt-ordinal.md)

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as declarações do utilizador são marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [Modelo.](/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO).

O código de criação de entidades cria uma entidade de aprendizagem automática com subentidades e características aplicadas às `Quantity` subentidades.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Screenshot parcial do portal mostrando a entidade criada, uma entidade de aprendizagem automática com subentidades e funcionalidades aplicadas às subentidades 'Quantidade'.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Coloque o seguinte método acima do `quickstart` método para encontrar o ID da sub-entidade de Quantidade, a fim de atribuir as funcionalidades a essa sub-entidade.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Adicione o exemplo da intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione palavras de exemplo criando uma lista de objetos [ExemploLabelObject,](/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) um objeto para cada palavra de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor pares de nomes e valor de entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão de exemplo.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Screenshot parcial mostrando a expressão de exemplo rotulada no portal. ":::

Chame [exemplos.adicione](/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-lates) com o ID da aplicação, iD da versão e o exemplo.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a app LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de encenação ou do produto.

O [método train.trainVersion](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart shows, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de sondagem para o método [get_status](/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicar app para slot de produção

Publique a aplicação LUIS utilizando o método [app.publish.](/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizador para previsão de intenção e extração de entidades.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Autenticar o cliente de tempo de execução da previsão

Use um objeto msRest.ApiKeyCredentials com a sua chave e use-o com o seu ponto final para criar um [LUIS. Objeto LUISRuntimeClient.](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte código para criar o pedido ao tempo de execução da previsão. A expressão do utilizador faz parte do objeto [predictionRequest.](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)

O método **[luisRuntimeClient.forecast.getSlotPrediction](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** necessita de vários parâmetros, tais como o ID da aplicação, o nome da slot e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node index.js` comando no seu ficheiro quickstart.

```console
node index.js
```