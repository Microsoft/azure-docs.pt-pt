---
title: 'Início rápido: Biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do Reconhecimento vocal (LUIS) para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Reconhecimento vocal (LUIS) permite que você aplique inteligência de aprendizado de máquina personalizada ao texto de linguagem natural de um usuário para prever o significado geral e extrair informações relevantes e detalhadas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: diberry
ms.openlocfilehash: 19eeb396536925d9667c8dddb09bea779de79555
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707190"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>Início rápido: Biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET

Introdução à biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Reconhecimento vocal (LUIS) permite que você aplique inteligência de aprendizado de máquina personalizada ao texto de linguagem natural de um usuário para prever o significado geral e extrair informações relevantes e detalhadas. 

Use a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET para:

* Criar uma aplicação
* Adicionar tentativas, entidades e declarações de exemplo
* Adicionar recursos, como uma lista de frases
* Treinar e publicar o aplicativo

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [ C# Exemplos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs) [de pacote de criação de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)  |  [código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | -fonte de biblioteca de documentação de referência (NuGet)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal do Reconhecimento vocal (LUIS)- [crie um gratuitamente](https://www.luis.ai)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-authoring-key"></a>Obter sua chave de criação de Reconhecimento vocal (LUIS)

Obtenha sua [chave de criação](luis-how-to-account-settings.md)e [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada. `COGNITIVESERVICE_AUTHORING_KEY`

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

1. Em uma janela de console (como cmd, PowerShell ou bash), use o comando dotnet `new` para criar um novo aplicativo de console com o nome `language-understanding-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de `Program.cs`origem:. 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. Altere o diretório para a pasta de aplicativos recém-criada. 

1. Você pode criar o aplicativo com:

    ```console
    dotnet build
    ```

    A saída da compilação não deve conter nenhum aviso ou erro. 
    
    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>Instalar o SDK

No diretório do aplicativo, instale a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring -Version 3.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.


## <a name="object-model"></a>Modelo de objeto

O cliente de criação de Reconhecimento vocal (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) que se autentica no Azure, que contém a sua chave de criação.

Depois que o cliente for criado, use esse cliente para acessar a funcionalidade, incluindo:

* Aplicativos – [criar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [excluir](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publicar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Exemplo declarações- [Adicionar por lote](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [excluir por ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 
* Modelo – gerenciar [tentativas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) e entidades
* Padrão-gerenciar [padrões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Treinar- [treinar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) -gerenciar com clonagem, exportação e exclusão


## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para .NET:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicionar exemplo declarações](#add-example-utterance-to-intent)
* [Treinar o aplicativo](#train-the-app)
* [Publicar o aplicativo](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no seu editor ou IDE preferido. Substitua o código `using` existente pelas seguintes `using` diretivas:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para gerenciar sua chave de criação extraída de uma variável de ambiente `COGNITIVESERVICES_AUTHORING_KEY`chamada. Se você criou a variável de ambiente depois que o aplicativo é iniciado, o editor, IDE ou Shell em execução precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente.

1. Crie variáveis para manter a região de criação e o ponto de extremidade. A região de sua chave de criação depende de onde você está criando. As [três regiões de criação](luis-reference-regions.md) são:

    * List`australiaeast`
    * Européia`westeurope`
    * EUA e outras regiões- `westus` (padrão)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) com sua chave e use-o com seu ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) .

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie um aplicativo LUIS para conter o modelo de NLP (processamento de idioma natural) que contém tentativas, entidades e declarações de exemplo. 

1. Crie um [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). O nome e a cultura do idioma são propriedades obrigatórias. 

1. Chame o método [apps. addasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) . A resposta é a ID do aplicativo. 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_de expressão do usuário. Um usuário pode fazer uma pergunta ou fazer uma instrução procurando uma resposta pretendida específica de um bot (ou outro aplicativo cliente). Exemplos de intenções estão reservando um vôo, perguntando o clima em uma cidade de destino e solicitando informações de contato para o atendimento ao cliente.   

Crie um [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o ModelCreateObject para o método [Model. AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) . A resposta é a ID da intenção.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, elas são encontradas na maioria dos aplicativos. A entidade extrai informações do expressão do usuário, necessárias para fullfilr a intenção do usuário. Há vários tipos de entidades [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) predefinidas e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo incluem [Number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md). 

Esse método addentities criou uma `Location` entidade simples com duas funções, uma `Class` entidade simples, uma `Flight` entidade composta e adiciona várias entidades predefinidas.

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, eles podem se aplicar a muitas intenções. Somente os declarações de usuário de exemplo são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [modelo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) . Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados), `model` geralmente contendo a palavra no namespace de [modelos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) . 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicionar expressão de exemplo à intenção

Para determinar a intenção e a extração de entidades de um expressão, o aplicativo precisa de exemplos de declarações. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas. 

Adicione o exemplo declarações criando uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) , um objeto para cada exemplo expressão. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve ser exatamente como aparece no texto do exemplo expressão. 

Chame [Examples. BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) com a ID do aplicativo, a ID da versão e a lista de exemplos. A chamada responde com uma lista de resultados. Você precisa verificar o resultado de cada exemplo para certificar-se de que ele foi adicionado com êxito ao modelo. 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
Os métodos createtotal e **CreateLabel** são métodos utilitárias para ajudá-lo a criar objetos.

## <a name="train-the-app"></a>Preparar a aplicação

Depois que o modelo é criado, o aplicativo LUIS precisa ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](luis-container-howto.md)ou [publicado](luis-how-to-publish-app.md) nos slots de preparo ou de produto. 

O método [Train. TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) precisa da ID do aplicativo e da ID da versão. 

Um modelo muito pequeno, como este guia de início rápido, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publicar um aplicativo Reconhecimento vocal

Publique o aplicativo LUIS usando o método [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) . Isso publica a versão treinada atual no slot especificado no ponto de extremidade. O aplicativo cliente usa esse ponto de extremidade para enviar declarações de usuário para previsão de extração de intenção e entidade.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `run` dotnet do diretório do aplicativo.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar, poderá excluir o aplicativo LUIS. A exclusão do aplicativo é feita com o método [apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) . Você também pode excluir o aplicativo do [portal do Luis](https://www.luis.ai). 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Usar o SDK do .net para consultar o ponto de extremidade de previsão](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [O que é a API de Reconhecimento vocal (LUIS)?](what-is-luis.md)
* [Novidades](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md)e [declarações de exemplo](luis-concept-utterance.md)e [entidades](luis-reference-prebuilt-entities.md) predefinidas
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
