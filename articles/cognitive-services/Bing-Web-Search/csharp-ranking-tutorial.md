---
title: Usando a classificação para mostrar resultados de pesquisa
titleSuffix: Azure Cognitive Services
description: Mostra como usar a resposta Bing RankingResponse para mostrar os resultados da pesquisa na ordem de classificação.
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75382332"
---
# <a name="build-a-console-app-search-client-in-c"></a>Construa um cliente de pesquisa de aplicativos de consola em C #

Este tutorial mostra como construir uma simples aplicação de consola .NET Core que permite aos utilizadores consultar o API de Pesquisa Web Bing e exibir resultados classificados.

Este tutorial mostra como:

- Faça uma consulta simples para a API de pesquisa web bing
- Resultados da consulta de exibição em ordem classificada

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar o tutorial, precisa de:

- Visual Studio. Se não o tiver, [faça o download e instale o Visual Studio 2017 Community Edition gratuito.](https://www.visualstudio.com/downloads/)
- Uma chave de subscrição para a API de Pesquisa web bing. Se não possui uma chave, [inscreva-se numa avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Criar um novo projeto de App consola

No Visual Studio, crie um projeto com `Ctrl`+`Shift`+`N`.

No diálogo **New Project,** clique em **Visual C# > Windows Classic Desktop > Console App (.NET Framework)**.

Nomeie a aplicação **MyConsoleSearchApp**e, em seguida, clique em **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Adicione o pacote JSON.net Nuget ao projeto

JSON.net permite trabalhar com as respostas json devolvidas pela API. Adicione o seu pacote NuGet ao seu projeto:

- Na **Solution Explorer** clique no direito do projeto e selecione Gerir **pacotes NuGet...**.
- No **separador Browse,** procure `Newtonsoft.Json`. Selecione a versão mais recente e, em seguida, clique em **Instalar**.
- Clique no botão **OK** na janela **Rever Alterações.**
- Feche o separador Estúdio Visual intitulado **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Adicione uma referência ao System.Web

Este tutorial depende `System.Web` da montagem. Adicione uma referência a este conjunto ao seu projeto:

- No **Solution Explorer,** clique à direita em **Referências** e selecione **Adicionar Referência...**
- Selecione **Assembles > Framework,** em seguida, desloque-se para baixo e verifique **System.Web**
- Selecione **OK**

## <a name="add-some-necessary-using-statements"></a>Adicione algumas declarações necessárias

O código neste tutorial requer três declarações adicionais. Adicione estas declarações `using` abaixo das declarações existentes no topo da **Program.cs:**

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Peça ao utilizador uma consulta

No **Solution Explorer,** abra **Program.cs.** Atualizar `Main()` o método:

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

- Pede ao utilizador uma consulta
- Chamadas `RunQueryAndDisplayResults(userQuery)` para executar a consulta e exibir os resultados
- Aguarda a entrada do utilizador de modo a evitar que a janela da consola se feche imediatamente.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Procure resultados de consulta utilizando a API de pesquisa web bing

Em seguida, adicione um método que questione a API e mostre os resultados:

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

- Cria `HttpClient` uma consulta à API de pesquisa web
- Define o `Ocp-Apim-Subscription-Key` cabeçalho HTTP, que Bing usa para autenticar o pedido
- Executa o pedido e usa JSON.net para desserializar os resultados
- Chamadas `DisplayAllRankedResults(responseObjects)` para exibir todos os resultados em ordem classificada

Certifique-se de definir `Ocp-Apim-Subscription-Key` o valor da sua chave de subscrição.

## <a name="display-ranked-results"></a>Resultados classificados de exibição

Antes de mostrar como exibir os resultados em ordem classificada, dê uma olhada numa resposta de pesquisa web de amostra:

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

O `rankingResponse` objeto JSON[(documentação)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)descreve a ordem de visualização adequada para os resultados da pesquisa. Inclui um ou mais dos seguintes grupos priorizados:

- `pole`: Os resultados da pesquisa para obter o tratamento mais visível (por exemplo, apresentado acima da linha principal e da barra lateral).
- `mainline`: Os resultados da pesquisa para visualizar na linha principal.
- `sidebar`: Os resultados da procura para visualizar na barra lateral. Se não houver barra lateral, exiba os resultados abaixo da linha principal.

A resposta de ranking JSON pode incluir um ou mais dos grupos.

Em **Program.cs,** adicione o seguinte método para visualizar os resultados em ordem devidamente classificada:

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

- Loops sobre `rankingResponse` os grupos que a resposta contém
- Exibe os itens em cada grupo, chamando`DisplaySpecificResults(...)`

Em **Program.cs,** adicione os dois seguintes métodos:

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

Estes métodos funcionam em conjunto para obter os resultados da pesquisa para a consola.

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

Leia mais sobre [o uso do ranking para exibir resultados](rank-results.md).
