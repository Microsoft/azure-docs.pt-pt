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
ms.date: 01/22/2020
ms.author: diberry
ms.openlocfilehash: 808fdfb04cbe3b2b9e5f2de0c653bb978196269c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748761"
---
Use a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para node. js para:

* Crie um aplicativo.
* Adicione intenções, entidades e exemplos de declarações.
* Adicione recursos, como uma lista de frases.
* Treine e publique um aplicativo.
* Excluir aplicativo

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [código fonte da Biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | Pacote de Autor [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), Pacote de Prazo de [Execução (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Pré-requisitos

* Reconhecimento vocal recurso de criação: [crie um no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-starter-key"></a>Obter sua chave de início do Reconhecimento vocal (LUIS)

Obtenha a [chave inicial](../luis-how-to-azure-subscription.md#starter-key) criando um recurso de criação de Luis. Mantenha sua chave e o ponto de extremidade da chave para a próxima etapa.

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

Usando sua chave e a região da chave, crie duas variáveis de ambiente para autenticação:

* `LUIS_AUTHORING_KEY` - A chave de recursos para autenticar os seus pedidos.
* `LUIS_AUTHORING_ENDPOINT` - O ponto final associado à sua chave.

Use as instruções para seu sistema operacional.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite o seu `.bash_profile`e adicione a variável ambiental:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalar a biblioteca NPM para criação de LUIS

No diretório do aplicativo, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de criação de Reconhecimento vocal (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) que se autentica no Azure, que contém a sua chave de criação.

Depois que o cliente for criado, use esse cliente para acessar a funcionalidade, incluindo:

* Aplicativos- [Adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [excluir](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publicar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemplo declarações- [Adicionar por lote](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [excluir por ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modelo – gerenciar [tentativas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) e entidades
* Padrão-gerenciar [padrões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Treinar- [treinar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) -gerenciar com clonagem, exportação e exclusão


## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para node. js:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicionar exemplo declarações](#add-example-utterance-to-intent)
* [Treinar o aplicativo](#train-the-app)
* [Publicar o aplicativo](#publish-a-language-understanding-app)
* [Excluir o aplicativo](#delete-a-language-understanding-app)
* [Listar aplicativos](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Crie um novo ficheiro de texto no seu editor preferido ou IDE nomeado `luis_authoring_quickstart.js`. Em seguida, adicione as seguintes dependências.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) com sua chave e use-o com seu ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) .

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie um aplicativo LUIS para conter o modelo de NLP (processamento de idioma natural) que contém tentativas, entidades e declarações de exemplo.

1. Crie um método [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) do objeto [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) para criar o aplicativo. O nome e a cultura do idioma são propriedades obrigatórias.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_de expressão do usuário. Um usuário pode fazer uma pergunta ou fazer uma instrução procurando uma resposta _pretendida_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções estão reservando um vôo, perguntando o clima em uma cidade de destino e solicitando informações de contato para o atendimento ao cliente.

Utilize o [método modelo.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) com o nome da intenção única e, em seguida, passe o ID da aplicação, id versão e novo nome de intenção.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, elas são encontradas na maioria dos aplicativos. A entidade extrai informações do expressão do usuário, necessárias para fullfilr a intenção do usuário. Há vários tipos de entidades [predefinidas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo incluem [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Este método **add_entities** criou uma entidade `Location` simples com duas funções, uma entidade `Class` simples, uma entidade `Flight` composta e acrescenta várias entidades pré-construídas.

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, eles podem se aplicar a muitas intenções. Somente os declarações de usuário de exemplo são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [modelo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) . Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Adicionar expressão de exemplo à intenção

Para determinar a intenção e a extração de entidades de um expressão, o aplicativo precisa de exemplos de declarações. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas.

Adicione o exemplo declarações criando uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) , um objeto para cada exemplo expressão. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve ser exatamente como aparece no texto do exemplo expressão.

Chame [Examples. batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) com a ID do aplicativo, a ID da versão e a lista de exemplos. A chamada responde com uma lista de resultados. Você precisa verificar o resultado de cada exemplo para certificar-se de que ele foi adicionado com êxito ao modelo.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Preparar a aplicação

Depois que o modelo é criado, o aplicativo LUIS precisa ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nos slots de preparo ou de produto.

O método [Train. trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) precisa da ID do aplicativo e da ID da versão.

Um modelo muito pequeno, como este guia de início rápido, será treinado muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de votação para o método [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

O treinamento de todos os modelos leva tempo. Use o operationResult para verificar o status de treinamento.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publicar um aplicativo Reconhecimento vocal

Publique o aplicativo LUIS usando o método [app. publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Isso publica a versão treinada atual no slot especificado no ponto de extremidade. O aplicativo cliente usa esse ponto de extremidade para enviar declarações de usuário para previsão de extração de intenção e entidade.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Excluir um aplicativo Reconhecimento vocal

Publique o aplicativo LUIS usando o método [app. deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) . Isso exclui o aplicativo atual.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Listar aplicativos de reconhecimento de linguagem

Obter uma lista de aplicativos associados à chave de reconhecimento de idioma

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Executar a aplicação

Faça o pedido com o comando `node luis_authoring_quickstart.js` no seu ficheiro de arranque rápido.

```console
node luis_authoring_quickstart.js
```

A saída da linha de comando do aplicativo é:

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
