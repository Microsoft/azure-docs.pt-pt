---
title: 'Início rápido: chamar seu ponto de C# extremidade de pesquisa personalizada do Bing usando | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar os resultados da C#pesquisa de sua instância do pesquisa personalizada do Bing no.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: maheshb
ms.openlocfilehash: 88a8cc0dbedeb9398637ab9a02323e9b2a6b8627
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975266"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usandoC# 

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito no C#, o API de pesquisa personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: criar sua primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2019 ou posterior](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
- O pacote NuGet [pesquisa personalizada do Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) . 
    - Em **Gerenciador de soluções** no Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo C# aplicativo de console no Visual Studio. Em seguida, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Crie as seguintes classes para armazenar os resultados da pesquisa retornados pelo API de Pesquisa Personalizada do Bing.

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

3. No método principal do seu projeto, crie variáveis para sua chave de assinatura API de Pesquisa Personalizada do Bing, a ID de configuração personalizada da sua instância de pesquisa e um termo de pesquisa.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Construa a URL de solicitação acrescentando seu termo de pesquisa ao parâmetro de consulta `q=` e a ID de configuração personalizada da sua instância de pesquisa para `customconfig=`. Separe os parâmetros com um caractere de `&`. `url` pode ser o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Crie um cliente de solicitação e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Execute a solicitação de pesquisa e obtenha a resposta como um objeto JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Processar e ver os resultados

3. Itere sobre o objeto de resposta para exibir informações sobre cada resultado da pesquisa, incluindo seu nome, URL e a data em que a página da Web foi rastreada pela última vez.

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
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
