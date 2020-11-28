---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: a79f164a1733c46a4cca807403a7ead8df446f0a
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303677"
---
Utilize as bibliotecas de clientes de Compreensão linguística (LUIS) para .NET para:
* Criar uma aplicação
* Adicione uma intenção, uma entidade aprendida com máquinas, com um exemplo de expressão
* Treinar e publicar app
* Runtime de predição da consulta

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  [Código fonte da](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) Biblioteca de [Autoria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) e Previsão [Autoria](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) e [Previsão](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) NuGet [Amostra C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) e [.NET Core CLI](/dotnet/core/tools/).
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a sua subscrição Azure, [crie um recurso de autoria de Compreensão de Idiomas](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que [criar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) para ligar a sua aplicação à autoria de Compreensão linguística. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido. Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet `new` para criar uma nova aplicação de consola com o nome `language-understanding-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Mude o seu diretório para a pasta de aplicações recém-criada.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

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

### <a name="install-the-nuget-libraries"></a>Instalar as bibliotecas NuGet

No diretório de aplicações, instale as bibliotecas clientes De Compreensão linguística (LUIS) para .NET com os seguintes comandos:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Modelo de objeto de autoria

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) que autentica a Azure, que contém a sua chave de autoria.

## <a name="code-examples-for-authoring"></a>Exemplos de código para autoria

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [criar,](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [excluir,](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) [publicar](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Exemplo de declarações - [adicionar,](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet) [excluir por ID](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Features - gerir [listas de frases](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modelo - gerir [intenções](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) e entidades
* Pattern - gerir [padrões](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Train - [treine](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) a app e a sondagem para [o estado da formação](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versões](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - gerir com clone, exportar e eliminar

## <a name="prediction-object-model"></a>Modelo de objeto de previsão

O cliente de previsão de idioma (LUIS) é um objeto [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) que autentica para Azure, que contém a sua chave de recursos.

## <a name="code-examples-for-prediction-runtime"></a>Exemplos de código para o tempo de execução da previsão

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Previsão por [encenação ou ranhura de produção](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Previsão por [versão](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Substitua o código existente `using` pelas `using` seguintes diretivas:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Adicione o código da placa de caldeira

1. Alterar a assinatura do `Main` método para permitir chamadas de async:

    ```csharp
    public static async Task Main()
    ```

1. Adicione o resto do código no `Main` método da `Program` classe, salvo especificação em contrário.

## <a name="create-variables-for-the-app"></a>Criar variáveis para a app

Crie dois conjuntos de variáveis: o primeiro conjunto que alterar, o segundo conjunto sai à medida que aparecem na amostra de código. 

1. Crie variáveis para manter os nomes das chaves de autoria e dos recursos.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Crie variáveis para manter os seus pontos finais, nome de aplicação, versão e nome de intenção.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

Uma aplicação LUIS contém o modelo de processamento de linguagem natural (NLP), incluindo intenções, entidades e palavras de exemplo.

Criar um [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). O nome e a cultura linguística são propriedades necessárias. Ligue para o método [Apps.AddAsync.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) A resposta é o ID da aplicação.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma aplicação LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_ de expressão do utilizador . Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _determinada de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo numa cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Crie um [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) com o nome da intenção única e depois passe o ID da aplicação, o ID da versão e o ModelCreateObject para o método [Model.AddIntentAsync.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) A resposta é a intenção de identificação.

O `intentName` valor é codificado em código rígido como `OrderPizzaIntent` parte das variáveis na secção Criar variáveis para a secção [de aplicações.](#create-variables-for-the-app)

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informações da expressão do utilizador, necessárias para preencher a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  As entidades pré-construídas comuns para adicionar à sua app incluem [número,](../luis-reference-prebuilt-number.md) [datatimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografiaV2,](../luis-reference-prebuilt-geographyv2.md) [ordinal.](../luis-reference-prebuilt-ordinal.md)

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as declarações de utilizadores exemplo são marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [Modelo.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO), normalmente contendo a palavra `model` no espaço de nomes dos [Modelos.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

O código de criação de entidades cria uma entidade de aprendizagem automática com subentidades e características aplicadas às `Quantity` subentidades.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Screenshot parcial do portal mostrando a entidade criada, uma entidade de aprendizagem automática com subentidades e funcionalidades aplicadas às subentidades 'Quantidade'.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Utilize o seguinte método para a classe para encontrar o ID da sub-entidade de Quantidade, a fim de atribuir as funcionalidades a essa sub-entidade.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Adicione o exemplo da intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione palavras de exemplo criando uma lista de objetos [ExemploLabelObject,](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) um objeto para cada palavra de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor pares de nomes e valor de entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão de exemplo.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Screenshot parcial mostrando a expressão de exemplo rotulada no portal. ":::

Call [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions?view=azure-dotnet) com o ID da aplicação, iD da versão e o exemplo.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a app LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de encenação ou do produto.

O método [Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart shows, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da aplicação deve incluir uma chamada de voto para o método [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicar app para slot de produção

Publique a aplicação LUIS utilizando o método [PublishAsync.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizador para previsão de intenção e extração de entidades.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autenticar o cliente de tempo de execução da previsão

Utilize um objeto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISRuntimeClient.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte código para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [PredictionRequest.](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)

O método **GetSlotPredictionAsync** necessita de vários parâmetros, tais como o ID da aplicação, o nome da slot, o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidaturas.

```dotnetcli
dotnet run
```