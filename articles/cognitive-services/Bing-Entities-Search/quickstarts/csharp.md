---
title: 'Quickstart: Envie um pedido de pesquisa para a Rest API usando C# - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido para a API de Pesquisa de Entidades Bing usando C#, e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: cd89f2ae13b10c83c3fc22023fc2e3cae1770c98
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650267"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Quickstart: Envie um pedido de pesquisa para a API de pesquisa de entidadebing usando C #

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Entidades Bing e veja a resposta JSON. Esta simples aplicação C# envia uma consulta de pesquisa de notícias para a API, e exibe a resposta. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Embora esta aplicação esteja escrita em C#, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.


## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017 ou mais tarde.](https://www.visualstudio.com/downloads/)

- O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet. Para instalar o pacote NuGet no Estúdio Visual:

   1. Clique no seu projeto no **Solution Explorer**.
   2. Selecione **Gerir pacotes NuGet**.
   3. Procure e selecione *Newtonsoft.Json*e, em seguida, instale o pacote.

- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova solução de consola C# no Estúdio Visual. Em seguida, adicione os seguintes espaços de nome no ficheiro de código principal:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Crie uma nova classe e adicione variáveis para o ponto final da API, a sua chave de subscrição e a consulta que pretende pesquisar. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Envie um pedido e obtenha a resposta da API

1. Dentro da classe, crie uma função chamada `Search()` . Dentro desta função, crie um novo `HttpClient` objeto e adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

2. Construa o URI para o seu pedido combinando o hospedeiro e o caminho. Em seguida, adicione o seu mercado e código URL a sua consulta.

3. Aguarde `client.GetAsync()` para obter uma resposta HTTP, e depois guarde a resposta jSON aguardando `ReadAsStringAsync()` .

4. Forforte a corda JSON `JsonConvert.DeserializeObject()` e imprima-a para a consola.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. No `Main()` método da sua aplicação, ligue para a `Search()` função.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Exemplo resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidades Bing?](../overview.md )
* [Referência a API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)de Pesquisa de Entidades Bing .
