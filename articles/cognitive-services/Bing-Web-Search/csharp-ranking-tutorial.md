---
title: Usando a classificação para exibir os resultados da pesquisa
titleSuffix: Azure Cognitive Services
description: Mostra como usar a resposta do Bing RankingResponse para exibir resultados da pesquisa em ordem de classificação.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382332"
---
# <a name="build-a-console-app-search-client-in-c"></a>Criar um cliente de pesquisa de aplicativo de console noC#

Este tutorial mostra como criar um aplicativo de console simples do .NET Core que permite aos usuários consultar os API de Pesquisa na Web do Bing e exibir os resultados classificados.

Este tutorial mostra como:

- Faça uma consulta simples para o API de Pesquisa na Web do Bing
- Exibir resultados da consulta em ordem classificada

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar o tutorial, você precisará de:

- Visual Studio. Se você não o tiver, [Baixe e instale o Visual Studio 2017 Community Edition gratuito](https://www.visualstudio.com/downloads/).
- Uma chave de assinatura para o API de Pesquisa na Web do Bing. Se não possui uma chave, [inscreva-se numa avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Criar um novo projeto de aplicativo de console

No Visual Studio, crie um projeto com `Ctrl`+`Shift`+`N`.

Na caixa de diálogo **novo projeto** , clique em **Visual C# > área de trabalho clássica do Windows > aplicativo de console (.NET Framework)** .

Nomeie o aplicativo **MyConsoleSearchApp**e clique em **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Adicionar o pacote NuGet do JSON.net ao projeto

JSON.net permite que você trabalhe com as respostas JSON retornadas pela API. Adicione seu pacote NuGet ao seu projeto:

- Em **Gerenciador de soluções** clique com o botão direito do mouse no projeto e selecione **gerenciar pacotes NuGet...** .
- Na guia **procurar** , procure `Newtonsoft.Json`. Selecione a versão mais recente e clique em **instalar**.
- Clique no botão **OK** na janela **revisar alterações** .
- Feche a guia do Visual Studio chamada **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Adicionar uma referência a System. Web

Este tutorial se baseia no assembly `System.Web`. Adicione uma referência a este assembly ao seu projeto:

- Em **Gerenciador de soluções**, clique com o botão direito do mouse em **referências** e selecione **Adicionar referência...**
- Selecione **Assemblies > Framework**e, em seguida, role para baixo e verifique **System. Web**
- Selecione **OK**

## <a name="add-some-necessary-using-statements"></a>Adicionar algumas instruções using necessárias

O código neste tutorial requer três instruções using adicionais. Adicione essas instruções abaixo das instruções de `using` existentes na parte superior de **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Solicitar ao usuário uma consulta

Em **Gerenciador de soluções**, abra **Program.cs**. Atualize o método de `Main()`:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Este método:

- Solicita ao usuário uma consulta
- Chama `RunQueryAndDisplayResults(userQuery)` para executar a consulta e exibir os resultados
- Aguarda a entrada do usuário para impedir que a janela do console seja fechada imediatamente.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Pesquisar resultados da consulta usando o API de Pesquisa na Web do Bing

Em seguida, adicione um método que consulta a API e exibe os resultados:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Este método:

- Cria um `HttpClient` para consultar a API de Pesquisa na Web
- Define o cabeçalho HTTP `Ocp-Apim-Subscription-Key`, que o Bing usa para autenticar a solicitação
- Executa a solicitação e usa JSON.net para desserializar os resultados
- Chama `DisplayAllRankedResults(responseObjects)` para exibir todos os resultados em ordem classificada

Certifique-se de definir o valor de `Ocp-Apim-Subscription-Key` para sua chave de assinatura.

## <a name="display-ranked-results"></a>Exibir resultados classificados

Antes de mostrar como exibir os resultados em ordem classificada, dê uma olhada em uma resposta de pesquisa na Web de exemplo:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

O objeto JSON `rankingResponse` ([documentação](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) descreve a ordem de exibição apropriada para os resultados da pesquisa. Ele inclui um ou mais dos seguintes grupos priorizados:

- `pole`: os resultados da pesquisa para obter o tratamento mais visível (por exemplo, exibido acima da principal e da barra lateral).
- `mainline`: os resultados da pesquisa a serem exibidos na principal.
- `sidebar`: os resultados da pesquisa a serem exibidos na barra lateral. Se não houver nenhuma barra lateral, exiba os resultados abaixo da principal.

A resposta de classificação JSON pode incluir um ou mais dos grupos.

No **Program.cs**, adicione o seguinte método para exibir os resultados em ordem corretamente classificada:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Este método:

- Executa um loop sobre os grupos de `rankingResponse` que a resposta contém
- Exibe os itens em cada grupo chamando `DisplaySpecificResults(...)`

No **Program.cs**, adicione os dois métodos a seguir:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Esses métodos funcionam juntos para gerar os resultados da pesquisa para o console.

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação. O resultado deve ter um aspeto semelhante ao seguinte:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre como [usar a classificação para exibir resultados](rank-results.md).
