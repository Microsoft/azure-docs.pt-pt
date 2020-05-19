---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando C# [ Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar resultados de pesquisa da sua instância de Pesquisa Personalizada Bing em C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1084c1962db3c04b951245361da80bee098329a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199825"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando C # 

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em C#, o Bing Custom Search API é um serviço web RESTful compatível com a maioria dos idiomas de programação. O código fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Para mais informações, consulte [Quickstart: Crie a sua primeira instância](quick-start.md)de pesquisa personalizada bing .
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core).
- Qualquer edição do [Visual Studio 2019 ou mais tarde.](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono](https://www.mono-project.com/).
- O pacote de nuget de [pesquisa personalizada bing.](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) 

   Para instalar este pacote no Estúdio Visual: 
     1. Clique no seu projeto no **Solution Explorer**e, em seguida, selecione **Gerir pacotes NuGet**. 
     2. Procure e selecione *Microsoft.Azure.CognitiveServices.Search.CustomSearch*, e, em seguida, instale o pacote.

   Quando instala o pacote Bing Custom Search NuGet, o Visual Studio também instala os seguintes pacotes:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.clientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma nova aplicação de consola C# no Estúdio Visual. Em seguida, adicione os seguintes pacotes ao seu projeto:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Crie as seguintes aulas para armazenar os resultados de pesquisa devolvidos pela API de Pesquisa Personalizada bing:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. No método principal do seu projeto, crie as seguintes variáveis para a sua chave de subscrição API de pesquisa personalizada, id de configuração personalizada da instância de pesquisa e termo de pesquisa:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Construa o URL de pedido, afinando o seu termo de pesquisa ao parâmetro de consulta e o ID de configuração personalizado da sua instância de `q=` pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Para o `url` valor variável, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie um cliente de pedido e adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Execute o pedido de pesquisa e obtenha a resposta como um objeto JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Processar e ver os resultados

- Iterado sobre o objeto de resposta para exibir informações sobre cada resultado de pesquisa, incluindo o seu nome, url, e a data em que a página web foi pela última vez rastejada.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
