---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372239"
---
Utilize a biblioteca de clientes autoras (LUIS) de autor de línguas para:

* Criar uma aplicação
* Adicionar intençãos, entidades e declarações de exemplo
* Adicionar funcionalidades como uma lista de frases
* Treinar e publicar app

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [código fonte da Biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | Pacote autor [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [ C# Amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal De Compreensão da Linguagem (LUIS) - [Criar uma gratuitamente](https://www.luis.ai)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenha a sua chave de arranque de Compreensão linguística (LUIS)

Obtenha a sua [chave de arranque](../luis-how-to-azure-subscription.md#starter-key) criando um recurso de autor LUIS. Mantenha a chave, e a região da chave para o próximo passo.

### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

Utilizando a sua chave, e a região para a chave, crie duas variáveis ambientais para autenticação:

* `COGNITIVESERVICE_AUTHORING_KEY` - A chave de recursos para autenticar os seus pedidos.
* `COGNITIVESERVICE_REGION` - A região associada à sua chave. Por exemplo, `westus`.

Utilize as instruções para o seu sistema operativo.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite o seu `.bash_profile`e adicione a variável ambiental:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="create-a-new-c-application"></a>Criar uma C# nova aplicação

Crie uma nova aplicação .NET Core no seu editor ou IDE preferido.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet `new` para criar uma nova aplicação de consola com o nome `language-understanding-quickstart`. Este comando cria um simples C# projeto "Hello World" com um único ficheiro fonte: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Mude o seu diretório para a pasta de aplicações recém-criada.

1. Pode construir a aplicação com:

    ```dotnetcli
    dotnet build
    ```

    A saída de construção não deve conter avisos ou erros.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>Instalar o SDK

No âmbito do diretório de aplicações, instale a biblioteca de clientes autoras de Compreensão linguística (LUIS) para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.


## <a name="object-model"></a>Modelo de objeto

O cliente autor de Linguagem (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) que autentica o Azure, que contém a sua chave de autoria.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [criar,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [excluir,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) [publicar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Declarações de exemplo - [adicionar por lote](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), excluir por [ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Características - gerencie [listas de frases](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modelo - gerir intenções e [entidades](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)
* Padrão - gerir [padrões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Comboio - [treine](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) a app e a sondagem para [o estado de formação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - gerencie com clone, exporte e elimine


## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de autor de Linguagem (LUIS) para .NET:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicione expressões de exemplo](#add-example-utterance-to-intent)
* [Treine a app](#train-the-app)
* [Publicar a app](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor ou IDE preferido. Substitua o código `using` existente pelas seguintes diretivas `using`:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie uma variável para gerir a sua chave de autor, retirada de uma variável ambiental chamada `COGNITIVESERVICES_AUTHORING_KEY`. Se criou a variável ambiental após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde.

1. Crie variáveis para manter a sua região de autor e ponto final. A região da sua chave de autor depende de onde é autor. As [três regiões autorais](../luis-reference-regions.md) são:

    * Austrália - `australiaeast`
    * Europa - `westeurope`
    * EUA e outras regiões - `westus` (Padrão)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Crie um objeto [ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie uma app LUIS para conter o modelo de processamento de linguagem natural (NLP) com intençãos, entidades e pronunciações de exemplo.

1. Criar uma [AplicaçãoCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). O nome e a cultura linguística são propriedades necessárias.

1. Ligue para o método [Apps.AddAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) A resposta é o ID da aplicação.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma app LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_de expressão do utilizador . Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _específica de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo em uma cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Crie um [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) com o nome da intenção única e passe o ID da aplicação, o ID da versão e o ModelCreateObject para o método [Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) A resposta é a identificação intencional.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informação da expressão do utilizador, necessária para plenar a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  Entidades pré-construídas comuns para adicionar à sua aplicação incluem [número](../luis-reference-prebuilt-number.md), [dataV2](../luis-reference-prebuilt-datetimev2.md), [geografiaV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Este método **AddEntities** criou uma entidade `Location` simples com duas funções, uma entidade `Class` simples, uma entidade `Flight` composta e acrescenta várias entidades pré-construídas.

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as expressões dos utilizadores exemplo estão marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [Modelo.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Cada tipo de entidade tem [o](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) seu próprio modelo de objeto de transformação de dados (DTO), geralmente contendo a palavra `model` no espaço de nome models.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicione a expressão do exemplo à intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione expressões exemplo criando uma lista de objetos [ExampleLabelObject,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) um objeto para cada expressão de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor par de nomes e valor da entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão do exemplo.

Ligue [para exemplos.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) com o ID da aplicação, id da versão e a lista de exemplos. A chamada responde com uma lista de resultados. É necessário verificar o resultado de cada exemplo para se certificar de que foi adicionado com sucesso ao modelo.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

Os métodos **CreateUtterance** e **CreateLabel** são métodos de utilidade para ajudá-lo a criar objetos.

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a aplicação LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de preparação ou produtos.

O método [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart mostra, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de votação para o método [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publicar uma aplicação de compreensão de linguagem

Publique a aplicação LUIS utilizando o método [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizadores para previsão de intenção e extração de entidades.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando `dotnet run` do seu diretório de aplicações.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar, pode eliminar a aplicação LUIS. A eliminação da aplicação é feita com o método [Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) Também pode eliminar a aplicação do [portal LUIS.](https://www.luis.ai)