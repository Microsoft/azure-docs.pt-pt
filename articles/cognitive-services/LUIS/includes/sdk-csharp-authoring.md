---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/26/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 559aee5de7dcf63c92957d70468db6c9672d9155
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84124453"
---
Utilize a biblioteca de clientes autoras (LUIS) de autor de línguas para:

* Criar uma aplicação
* Adicionar intençãos, entidades e declarações de exemplo
* Adicionar funcionalidades como uma lista de frases
* Treinar e publicar app

[Documentação de](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring)  |  [Pacote de autoria (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)  |  [Amostras C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua assinatura Azure, [crie um recurso de autor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) de Compreensão linguística no portal Azure para obter a sua chave e ponto final. Aguarde que se desloque e clique no botão **De recurso Go.**
    * Necessitará da chave e do ponto final do recurso que [cria](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) para ligar a sua aplicação à autoria da Compreensão da Linguagem. Vaicolar a chave e o ponto final no código abaixo no arranque rápido. Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor ou IDE preferido.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet para criar uma nova aplicação de `new` consola com o nome `language-understanding-quickstart` . Este comando cria um simples projeto C# "Hello World" com um único ficheiro de origem: `Program.cs` .

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
* [Adicionar expressões de exemplo](#add-example-utterance-to-intent)
* [Treine a app](#train-the-app)
* [Publicar a aplicação](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor ou IDE preferido. Substitua o código existente `using` pelas `using` seguintes diretivas:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie uma variável para segurar a sua chave de autor e o ponto final de autoria.

    [!code-csharp[Authorization to resource key](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Variables)]

1. Crie um objeto [ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie uma app LUIS para conter o modelo de processamento de linguagem natural (NLP) com intençãos, entidades e pronunciações de exemplo.

1. Criar uma [AplicaçãoCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). O nome e a cultura linguística são propriedades necessárias.

1. Ligue para o método [Apps.AddAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) A resposta é o ID da aplicação.

    [!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma app LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_de expressão do utilizador . Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _específica de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo em uma cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Crie um [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) com o nome da intenção única e passe o ID da aplicação, o ID da versão e o ModelCreateObject para o método [Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) A resposta é a identificação intencional.

[!code-csharp[Create intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informação da expressão do utilizador, necessária para plenar a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  Entidades pré-construídas comuns para adicionar à sua aplicação incluem [número](../luis-reference-prebuilt-number.md), [dataV2](../luis-reference-prebuilt-datetimev2.md), [geografiaV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Este método **AddEntities** criou uma `Location` entidade simples com duas funções, uma entidade `Class` simples, uma entidade composta e acrescenta `Flight` várias entidades pré-construídas.

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as expressões dos utilizadores exemplo estão marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [Modelo.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO), geralmente contendo a palavra `model` no espaço de nome [models.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicione a expressão do exemplo à intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione expressões exemplo criando uma lista de objetos [ExampleLabelObject,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) um objeto para cada expressão de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor par de nomes e valor da entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão do exemplo.

Ligue [para exemplos.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) com o ID da aplicação, id da versão e a lista de exemplos. A chamada responde com uma lista de resultados. É necessário verificar o resultado de cada exemplo para se certificar de que foi adicionado com sucesso ao modelo.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringBatchAddUtterancesForIntent)]

Os métodos **CreateUtterance** e **CreateLabel** são métodos de utilidade para ajudá-lo a criar objetos.

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a aplicação LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de preparação ou produtos.

O método [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart mostra, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de votação para o método [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-csharp[Train the app's version](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publicar uma aplicação de compreensão de linguagem

Publique a aplicação LUIS utilizando o método [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizadores para previsão de intenção e extração de entidades.

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidatura.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar, pode eliminar a aplicação LUIS. A eliminação da aplicação é feita com o método [Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) Também pode eliminar a aplicação do [portal LUIS.](https://www.luis.ai)