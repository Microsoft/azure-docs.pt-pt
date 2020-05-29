---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 6e240a0c5d5d77489c92862238c2e5041bdeabe3
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171357"
---
Utilize a biblioteca de clientes de compreensão linguística (LUIS) para:

* Criar uma aplicação.
* Adicione intenções, entidades e exemplos de declarações.
* Adicione funcionalidades, como uma lista de frases.
* Treine e publique uma aplicação.
* Excluir app

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)  |  [Pacote de autoria (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), Amostras de pacote de tempo de [execução (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Pré-requisitos

* Recurso de autoria de compreensão [linguística: Criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenha a sua chave de arranque de compreensão linguística (LUIS)

Obtenha a sua [chave de arranque](../luis-how-to-azure-subscription.md#starter-key) criando um recurso de autoria LUIS. Guarde a chave e o ponto final da chave para o próximo passo.

### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

Utilizando a sua chave, e a região para a chave, crie duas variáveis ambientais para a autenticação:

* `LUIS_AUTHORING_KEY`- A chave de recursos para autenticar os seus pedidos.
* `LUIS_AUTHORING_ENDPOINT`- O ponto final associado à sua chave.

Utilize as instruções para o seu sistema operativo.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite a sua `.bash_profile` variável e adicione a variável ambiental:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalar a biblioteca NPM para a autoria do LUIS

No diretório de aplicações, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) que autentica a Azure, que contém a sua chave de autoria.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [adicionar,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-) [excluir,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) [publicar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemplo de declarações - [adicionar por lote](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), excluir por [ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Features - gerir [listas de frases](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modelo - gerir [intenções](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) e entidades
* Pattern - gerir [padrões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Train - [treine](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) a app e a sondagem para [o estado da formação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - gerir com clone, exportar e eliminar


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de autoria de Language Understanding (LUIS) para Node.js:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicionar expressões de exemplo](#add-example-utterance-to-intent)
* [Treine a aplicação](#train-the-app)
* [Publicar a aplicação](#publish-a-language-understanding-app)
* [Eliminar a aplicação](#delete-a-language-understanding-app)
* [Lista de aplicativos](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Crie um novo ficheiro de texto no seu editor preferido ou IDE nomeado `luis_authoring_quickstart.js` . Em seguida, adicione as seguintes dependências.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Dependencies)]

Crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie uma app LUIS para conter o modelo de processamento de linguagem natural (NLP) que detém intenções, entidades e palavras de exemplo.

1. Crie um método de [adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) de um objeto [appsoperação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) para criar a aplicação. O nome e a cultura linguística são propriedades necessárias.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma aplicação LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_de expressão de utilizador. Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _determinada de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo numa cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Utilize o [método modelo.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) com o nome da intenção única e depois passe o ID da aplicação, o ID da versão e o novo nome de intenção.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informações da expressão do utilizador, necessárias para preencher a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  As entidades pré-construídas comuns para adicionar à sua app incluem [número,](../luis-reference-prebuilt-number.md) [datatimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografiaV2,](../luis-reference-prebuilt-geographyv2.md) [ordinal.](../luis-reference-prebuilt-ordinal.md)

Este **método add_entities** criou uma entidade simples com `Location` duas funções, uma `Class` entidade simples, uma entidade composta e adiciona `Flight` várias entidades pré-construídas.

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as declarações do utilizador são marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [Modelo.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Adicione o exemplo da intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione palavras de exemplo criando uma lista de objetos [ExemploLabelObject,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) um objeto para cada palavra de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor pares de nomes e valor de entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão de exemplo.

Chame [exemplos.lote](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) com o ID da aplicação, iD da versão e a lista de exemplos. A chamada responde com uma lista de resultados. É necessário verificar o resultado de cada exemplo para se certificar de que foi adicionado com sucesso ao modelo.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a app LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de encenação ou do produto.

O [método train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart shows, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de sondagem para o método [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Treinar todos os modelos leva tempo. Utilize a operaçãoResult para verificar o estado do treino.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publicar uma aplicação de compreensão de idiomas

Publique a aplicação LUIS utilizando o método [app.publish.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizador para previsão de intenção e extração de entidades.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Excluir uma aplicação de compreensão de idiomas

Elimine a aplicação LUIS utilizando o método [app.deleteMethod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) Isto elimina a aplicação atual.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Aplicativos de compreensão de linguagem de lista

Obtenha uma lista de aplicações associadas à chave de compreensão de idioma

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node luis_authoring_quickstart.js` comando no seu ficheiro quickstart.

```console
node luis_authoring_quickstart.js
```

A saída da linha de comando da aplicação é:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
