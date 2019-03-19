---
title: 'Início rápido: Chamar a sua através de ponto final de pesquisa personalizada do Bing C# | Documentos da Microsoft'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing no C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: a775c1c864a8a5513be546195da5c0891f8bb1f6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849402"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Início rápido: Chamar a sua através de ponto final de pesquisa personalizada do BingC# 

Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing. Embora esse aplicativo é escrito em C#, a API de pesquisa personalizada do Bing é um serviço RESTful web compatível com a maioria das linguagens de programação. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada do Bing. Consulte [início rápido: Criar a primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
- O pacote de [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)instalado. 
    - No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo aplicativo de console do c# no Visual Studio. Em seguida, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Crie as seguintes classes para armazenar os resultados da pesquisa devolvidos pela API de pesquisa personalizada do Bing.

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

3. O método principal de seu projeto, crie variáveis para a chave de subscrição de API de pesquisa personalizada do Bing, ID de configuração personalizada da sua instância de pesquisa e um termo de pesquisa.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Construir o URL do pedido ao acrescentar o termo de pesquisa para o `q=` parâmetro de consulta e o ID de configuração da sua instância de pesquisa personalizada para `customconfig=`. Separe-os com um `&` caráter. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Criar um cliente de solicitação e adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Efetuar o pedido de pesquisa e obtenha a resposta como um objeto JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

1. Itere sobre o objeto de resposta para apresentar informações sobre cada resultado da pesquisa, incluindo o respetivo nome, url e a data, a página Web foi rastreada pela última vez.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
