---
title: Utilizar a classificação para apresentar os resultados da pesquisa
titleSuffix: Azure Cognitive Services
description: Mostra como usar a resposta do Bing RankingResponse para exibir os resultados da pesquisa na ordem de classificação.
services: cognitive-services
author: bradumbaugh
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: bking
ms.openlocfilehash: 6afaa2f497fa1a23d083c3ddd76359a49105732d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384847"
---
# <a name="build-a-console-app-search-client-in-c"></a>Criar um cliente de pesquisa de aplicação de consola em c#

Este tutorial mostra como criar uma aplicação de consola .NET Core simple que permite aos utilizadores consultar a API de pesquisa Web Bing e exibir resultados classificados.

Este tutorial mostra como:

- Fazer uma consulta simples para a API de pesquisa Web Bing
- Apresentar os resultados da consulta em ordem classificada

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar o tutorial, terá de:

- Visual Studio. Se não tiver, [transferir e instalar o Visual Studio 2017 Community Edition gratuita](https://www.visualstudio.com/downloads/).
- Uma chave de subscrição para a API de pesquisa Web Bing. Se não possui uma chave, [inscreva-se numa avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Criar um novo projeto de aplicação de consola

No Visual Studio, crie um projeto com `Ctrl`+`Shift`+`N`.

Na **novo projeto** caixa de diálogo, clique em **Visual c# > área de trabalho clássica do Windows > aplicação de consola (.NET Framework)** .

Dê o nome **MyConsoleSearchApp**e, em seguida, clique em **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Adicione o pacote JSON.net Nuget ao projeto

JSON.net permite-lhe trabalhar com as respostas JSON devolvidas pela API. Adicione o seu pacote NuGet ao seu projeto:

- Na **Explorador de soluções** com o botão direito no projeto e selecione **gerir pacotes NuGet...** .
- Sobre o **navegue** separador, procure `Newtonsoft.Json`. Selecione a versão mais recente e, em seguida, clique em **instalar**.
- Clique nas **OK** botão a **rever alterações** janela.
- Feche o separador do Visual Studio intitulado **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Adicionar uma referência a System. Web

Este tutorial baseia-se no `System.Web` assembly. Adicione uma referência a esse assembly ao seu projeto:

- Na **Explorador de soluções**, clique em **referências** e selecione **Add Reference...**
- Selecione **Assemblies > Framework**, em seguida, desloque para baixo e verificar **System. Web**
- Selecione **OK**

## <a name="add-some-necessary-using-statements"></a>Adicionar algumas necessárias usando instruções

O código neste tutorial requer três adicionais usando instruções. Adicionar essas instruções abaixo existente `using` declarações na parte superior **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Pedir ao utilizador para uma consulta

Na **Explorador de soluções**, abra **Program.cs**. Atualização do `Main()` método:

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

- Pede ao utilizador para uma consulta
- Chamadas `RunQueryAndDisplayResults(userQuery)` para executar a consulta e exibir os resultados
- Tem de aguardar pela intervenção do utilizador para impedir que a janela da consola imediatamente a fechar.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Procurar resultados de consulta usando a API de pesquisa Web Bing

Em seguida, adicione um método que a API de consulta e apresenta os resultados:

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

- Cria um `HttpClient` para consultar a API de pesquisa Web
- Conjuntos de `Ocp-Apim-Subscription-Key` cabeçalho HTTP, que o Bing utiliza para autenticar o pedido
- Executa a solicitação e utiliza o JSON.net para anular a serialização de resultados
- Chamadas `DisplayAllRankedResults(responseObjects)` para apresentar todos os resultados em ordem classificada

Certifique-se de definir o valor de `Ocp-Apim-Subscription-Key` à sua chave de subscrição.

## <a name="display-ranked-results"></a>Exibir resultados classificados

Antes de mostrar como exibir os resultados em ordem classificada, dê uma olhada numa resposta de pesquisa de web de exemplo:

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

O `rankingResponse` objeto JSON ([documentação](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) descreve a ordem de exibição apropriado para resultados da pesquisa. Ele inclui um ou mais dos seguintes, prioritários grupos:

- `pole`: Os resultados da pesquisa para obter o tratamento mais visível (por exemplo, são apresentados acima o principal e barra lateral).
- `mainline`: Os resultados da pesquisa para apresentar os principais no.
- `sidebar`: Os resultados da pesquisa para apresentar na barra lateral. Se não houver nenhuma barra lateral, exiba os resultados abaixo as principais.

A resposta de classificação JSON pode incluir um ou mais dos grupos.

Na **Program.cs**, adicione o seguinte método para exibir resultados em ordem classificada corretamente:

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

- Faz um loop sobre o `rankingResponse` grupos que contém a resposta
- Apresenta os itens em cada grupo chamando `DisplaySpecificResults(...)`

Na **Program.cs**, adicione os seguintes dois métodos:

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

Esses métodos funcionam em conjunto para enviar os resultados de pesquisa para a consola.

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

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre [com a classificação de mensagens em fila para apresentar resultados](rank-results.md).
