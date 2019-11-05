---
title: 'Início rápido C# : ponto de extremidade de previsão de consulta do SDK-Luis'
titleSuffix: Azure Cognitive Services
description: Use o C# SDK para enviar um usuário expressão para Luis e receber uma previsão.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d7cf46add464a089f15a5bd15a20cea7d17eed37
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488682"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>Início rápido: consulta de ponto de extremidade C# de previsão V2 com SDK do .net

Use o SDK do .NET, encontrado no [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), para enviar um usuário expressão para reconhecimento vocal (Luis) e receber uma previsão da intenção do usuário. 

Este início rápido envia um expressão de usuário, como `turn on the bedroom light`, para um aplicativo público Reconhecimento vocal, em seguida, recebe a previsão e exibe a intenção de pontuação superior `HomeAutomation.TurnOn` e a entidade `HomeAutomation.Room` encontrada dentro do expressão. 

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio Community – edição de 2017](https://visualstudio.microsoft.com/vs/community/)
* Linguagem de programação C# (incluída com o VS Community 2017)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> A solução completa está disponível no repositório do GitHub [cognitiva-Services-Language-Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) .

Procurando mais documentação?

 * [Documentação de referência do SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Obter serviços cognitivas ou chave de Reconhecimento vocal

Para usar o aplicativo público para a automação inicial, você precisa de uma chave válida para previsões de ponto de extremidade. Você pode usar uma chave de serviços cognitivas (criada abaixo com o CLI do Azure), que é válido para muitos serviços cognitivas ou uma chave de `Language Understanding`. 

Use o seguinte [comando CLI do Azure para criar uma chave de serviço cognitiva](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Criar projeto do .NET Core

Crie um projeto de console do .NET Core no Visual Studio Community 2017.

1. Abra o Visual Studio Community 2017.
1. Crie um novo projeto, na seção **Visual C#**  , escolha **aplicativo de console (.NET Core)** .
1. Insira o nome do projeto `QueryPrediction`, deixe os valores padrão restantes e selecione **OK**.
    Isso cria um projeto simples com o arquivo de código primário chamado **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Adicionar SDK com NuGet

1. No **Gerenciador de soluções**, selecione o projeto no modo de exibição de árvore chamado **QueryPrediction**e clique com o botão direito do mouse em. No menu, selecione **gerenciar pacotes NuGet...** .
1. Selecione **procurar** e insira `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Quando as informações do pacote forem exibidas, selecione **instalar** para instalar o pacote no projeto. 
1. Adicione as seguintes instruções _using_ à parte superior de **Program.cs**. Não remova a instrução _using_ existente para `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Criar um novo método para a previsão

Crie um novo método, `GetPrediction` para enviar a consulta ao ponto de extremidade de previsão de consulta. O método criará e configurará todos os objetos necessários e, em seguida, retornará um `Task` com os resultados da previsão de [`LuisResult`](https://docs.microsoft.com/en-us/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-python) . 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Criar objeto de credenciais

Adicione o código a seguir ao método `GetPrediction` para criar as credenciais do cliente com sua chave de serviço cognitiva.

Substitua `<REPLACE-WITH-YOUR-KEY>` pela região da sua chave de serviço cognitiva. A chave está no [portal do Azure](https://portal.azure.com) na página de chaves desse recurso.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Criar Reconhecimento vocal cliente

No método `GetPrediction`, após o código anterior, adicione o seguinte código para usar as novas credenciais, criando um objeto de cliente [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) . 

Substitua `<REPLACE-WITH-YOUR-KEY-REGION>` pela região da sua chave, como `westus`. A região de chave está no [portal do Azure](https://portal.azure.com) na página Visão geral desse recurso.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Definir parâmetros de consulta

No método `GetPrediction`, após o código anterior, adicione o código a seguir para definir os parâmetros de consulta.

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

No método `GetPrediction`, após o código anterior, adicione o seguinte código para definir os parâmetros de consulta:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Exibir resultados da previsão

Altere o método **Main** para chamar o novo método `GetPrediction` e retornar os resultados da previsão:

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

Compile o projeto no estúdio e execute o projeto para ver a saída da consulta:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [SDK do .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) e a [documentação de referência do .net](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Tutorial: compilar o aplicativo LUIS para determinar as intenções do usuário](luis-quickstart-intents-only.md) 