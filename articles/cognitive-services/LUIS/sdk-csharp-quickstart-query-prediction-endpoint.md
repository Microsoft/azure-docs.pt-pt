---
title: 'Início rápido: C#Ponto final de predição de consulta do SDK'
titleSuffix: Azure Cognitive Services
description: Utilize o C# SDK para enviar uma expressão de utilizador para LUIS e receber uma predição.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: diberry
ms.openlocfilehash: 086f55094474d4c06e52001d77630932cd04213c
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404098"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Início rápido: Consultar o ponto final de previsão com o C# .NET SDK

Utilizar o SDK .NET, foi encontrado na [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), para enviar uma expressão de utilizador para compreensão de idiomas (LUIS) e receber uma predição de intenção do utilizador. 

Neste início rápido, envia uma expressão de utilizador, tal como `turn on the bedroom light`, para um aplicativo de compreensão de idiomas público, em seguida, recebe a predição e exibe a intenção da parte superior de classificação `HomeAutomation.TurnOn` e a entidade `HomeAutomation.Room` encontrado dentro da expressão. 

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio Community – edição de 2017](https://visualstudio.microsoft.com/vs/community/)
* Linguagem de programação C# (incluída com o VS Community 2017)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> A solução completa está disponível a partir da [cognitivos-services--compreensão de idiomas](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) repositório do GitHub.

À procura de mais documentação?

 * [Documentação de referência do SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Obter chave de serviços cognitivos ou compreensão de idiomas

Para utilizar a aplicação pública para automação residencial, precisa de uma chave válida para previsões de ponto final. Pode utilizar qualquer dos serviços cognitivos chave (criado abaixo com a CLI do Azure), que é válido para muitos serviços cognitivos, ou um `Language Understanding` chave. 

Utilize o seguinte procedimento [comando da CLI do Azure para criar uma chave de serviço cognitivo](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Criar o projeto .NET Core

Crie um projeto de consola .NET Core no Visual Studio Community 2017.

1. Abra o Visual Studio Community 2017.
1. Criar um novo projeto, a partir da **Visual C#**  secção, escolha **a aplicação de consola (.NET Core)**.
1. Introduza o nome do projeto `QueryPrediction`, deixe os valores predefinidos restantes e selecione **OK**.
    Esta ação cria um projeto simples com o arquivo de código principal chamado **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Adicionar o SDK com o NuGet

1. Na **Explorador de soluções**, selecione o projeto na vista de árvore com o nome **QueryPrediction**, em seguida, clique com botão direito. No menu, selecione **gerir pacotes NuGet...** .
1. Selecione **navegue** , em seguida, introduza `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Quando exibe as informações do pacote, selecione **instalar** para instalar o pacote para o projeto. 
1. Adicione as seguintes _usando_ declarações na parte superior do **Program.cs**. Não remova o existente _usando_ instrução para `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Criar um novo método para a predição

Criar um novo método, `GetPrediction` para enviar a consulta para o ponto final de predição de consulta. O método irá criar e configurar todos os objetos necessários, em seguida, retornar uma `Task` com o [ `LuisResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) resultados da predição. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Criar o objeto de credenciais

Adicione o seguinte código para o `GetPrediction` método para criar as credenciais de cliente com a sua chave de serviço cognitivo.

Substitua `<REPLACE-WITH-YOUR-KEY>` com a região de sua chave de serviço cognitivo. A chave está no [portal do Azure](https://portal.azure.com) na página chaves para esse recurso.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Criar o cliente de compreensão de idiomas

Na `GetPrediction` método, após o código anterior, adicione o seguinte código para utilizar as novas credenciais, criação de um [ `LUISRuntimeClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) objeto cliente. 

Substitua `<REPLACE-WITH-YOUR-KEY-REGION>` com a região de sua chave, tais como `westus`. Esta chave região está na [portal do Azure](https://portal.azure.com) na página de descrição geral para esse recurso.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Definir parâmetros de consulta

Na `GetPrediction` método, após o código anterior, adicione o seguinte código para definir os parâmetros de consulta.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Ponto final de predição da consulta

Na `GetPrediction` método, após o código anterior, adicione o seguinte código para definir os parâmetros de consulta:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Exibir resultados de predição

Alteração da **Main** método para chamar a nova `GetPrediction` resulta de método e voltar a predição:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Executar o projeto

Compilar o projeto no Studio e executar o projeto para ver o resultado da consulta:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) e o [documentação de referência de .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Tutorial: Criar aplicação LUIS para determinar as intenções dos usuários](luis-quickstart-intents-only.md) 