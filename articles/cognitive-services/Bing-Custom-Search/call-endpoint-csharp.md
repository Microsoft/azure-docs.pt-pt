---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando C# Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar os resultados da pesquisa a partir da sua instância de Pesquisa Personalizada Bing em C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0abd03338982c866c92a8462e1e840651a9c2759
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101787"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando C # 

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em C#, a Bing Custom Search API é um serviço web RESTful compatível com a maioria das linguagens de programação. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Para obter mais informações, consulte [Quickstart: Crie a sua primeira instância de Pesquisa Personalizada Bing](quick-start.md).
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core).
- Qualquer edição do [Visual Studio 2019 ou posterior.](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono.](https://www.mono-project.com/)
- O pacote [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet. 

   Para instalar este pacote no Visual Studio: 
     1. Clique com o botão direito no seu projeto no **Solution Explorer** e, em seguida, selecione **Gerir pacotes NuGet** . 
     2. Procure e selecione *Microsoft.Azure.CognitiveServices.Search.CustomSearch* e, em seguida, instale o pacote.

   Quando instala o pacote Bing Custom Search NuGet, o Visual Studio também instala os seguintes pacotes:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma nova aplicação de consola C# no Visual Studio. Em seguida, adicione os seguintes pacotes ao seu projeto:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Crie as seguintes classes para armazenar os resultados de pesquisa devolvidos pela API de Pesquisa Personalizada Bing:

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

3. No método principal do seu projeto, crie as seguintes variáveis para a sua chave de subscrição de API de pesquisa personalizada Bing, iD de configuração personalizada de instância de pesquisa e termo de pesquisa:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Construa o URL de pedido, anexando o seu termo de pesquisa ao `q=` parâmetro de consulta, e o ID de configuração personalizado da sua instância de pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Para o `url` valor variável, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [personalizado subdomínio](../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie um cliente de pedido e adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

- Iterate sobre o objeto de resposta para exibir informações sobre cada resultado de pesquisa, incluindo o seu nome, url, e a data em que a página foi rastreada pela última vez.

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
> [Construa um aplicativo web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
