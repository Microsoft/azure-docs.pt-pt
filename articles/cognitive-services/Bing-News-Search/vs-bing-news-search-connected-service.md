---
title: Ligar à API com serviços ligados no Visual Studio de pesquisa de notícias do Bing eC#
titleSuffix: Azure Cognitive Services
description: Ligue à Pesquisa de Notícias do Bing a partir de uma aplicação Web ASP.NET Core.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 2925ca5a303876a68b6d605c7312d43af102b6e0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088473"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Tutorial: Ligar à API com serviços ligados no Visual Studio de pesquisa de notícias do Bing eC#

Ao utilizar a Pesquisa de Notícias do Bing, pode ativar aplicações e serviços para tirar partido do poder de um motor de busca sem anúncios e direcionado para a Web. A Pesquisa de Notícias do Bing é um dos serviços de pesquisa disponíveis com os Serviços Cognitivos.

Este artigo fornece detalhes para utilizar a funcionalidade de Serviço Ligado do Visual Studio para a Pesquisa de Notícias do Bing. A função está disponível no Visual Studio 2017 15.7 e posterior, com a extensão dos Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versão 15.7, com a carga de trabalho de Desenvolvimento Web instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Adicionar suporte ao seu projeto para a API de Pesquisa de Notícias do Bing

1. Crie um novo projeto Web ASP.NET Core com o nome MyWebApplication. Utilize o modelo de projeto **Aplicação Web (Model-View-Controller)**, com todas as predefinições. É importante que atribua o nome MyWebApplication ao projeto, para que o espaço de nomes corresponda ao copiar código para o projeto. 

1. No **Explorador de Soluções**, escolha **Adicionar** > **Serviço Ligado**.
   É apresentada a página Serviço Ligado, com os serviços que pode adicionar ao seu projeto.

   ![Captura de ecrã do item de menu Adicionar Serviço Ligado](../media/vs-common/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **Leve a Pesquisa Inteligente às suas Aplicações**.

   ![Captura de ecrã da lista de serviços ligados](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Se já tem sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, é apresentada uma página com uma lista pendente com as suas subscrições. Selecione a subscrição que pretende utilizar e, em seguida, escolha um nome para a API de Pesquisa de Notícias do Bing. Também pode escolher **Editar** para modificar o nome gerado automaticamente.

   ![Captura de ecrã dos campos subscrição e nome](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Escolha o grupo de recursos e o escalão de preço.

   ![Captura de ecrã dos campos grupo de recursos e escalão de preço](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Se pretender obter mais detalhes sobre os escalões de preço, selecione **Rever os preços**.

1. Escolha **Adicionar** para adicionar suporte para o Serviço Ligado.
   O Visual Studio modifica o seu projeto para adicionar os pacotes NuGet, as entradas do ficheiro de configuração e outras alterações para suportar uma ligação à API de Pesquisa de Notícias do Bing. A janela de saída mostra o registo do que está a acontecer ao seu projeto. Deverá ver algo semelhante ao seguinte:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   O ficheiro appsettings.json contém agora as seguintes definições novas:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Utilizar a API de Pesquisa de Notícias do Bing para adicionar funcionalidades de pesquisa a uma página Web

Agora que adicionou o suporte da API de Pesquisa de Notícias do Bing ao seu projeto, eis como utilizar a API para adicionar a pesquisa inteligente a uma página Web.

1. Em *Startup.cs*, no método `ConfigureServices`, adicione uma chamada para `IServiceCollection.AddSingleton`. Desta forma, o objeto de configuração que contém as definições de chaves fica disponível para o código no seu projeto.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. Adicione um novo ficheiro de classe na pasta **Modelos**, com o nome *BingNewsModel.cs*. Se tiver atribuído um nome diferente ao projeto, utilize o espaço de nomes do seu projeto, em vez de MyWebApplication. Substitua os conteúdos pelo seguinte código:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Este modelo é utilizado para armazenar os resultados de uma chamada ao serviço Pesquisa de Notícias do Bing.
 
1. Na pasta **Controladores**, adicione um novo ficheiro de classe com o nome *IntelligentSearchController.cs*. Substitua os conteúdos pelo seguinte código:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Neste código, o construtor configura o objeto de configuração que contém as chaves. O método para a `Search` ser encaminhada é apenas um redirecionamento para a função `BingSearchResult`. Isto chama o método `GetNewsSearchClient` para obter o objeto de cliente da `NewsSearchAPI`.  O objeto de cliente da `NewsSearchAPI` contém o método `SearchAsync`, que efetivamente chama o serviço e devolve os resultados no modelo `SearchResult` que acabou de criar. 

1. Adicione uma classe, `MyHandler`, que foi referenciada no código anterior. Isto delega a chamada assíncrona para o serviço de pesquisa à sua classe base, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Para adicionar suporte para submeter pesquisas e visualizar os resultados, na pasta **Vistas**, crie uma nova pasta com o nome **IntelligentSearch**. Nesta nova pasta, adicione uma vista *BingSearchResult.cshtml*. Copie o seguinte código:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Inicie a aplicação Web localmente, introduza o URL da página que acabou de criar (/IntelligentSearch/BingSearchResult) e publique um pedido de pesquisa com o botão Pesquisar.

   ![Captura de ecrã dos resultados de Pesquisa de Notícias do Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos já não for necessário, pode eliminá-lo. Esta ação elimina o serviço cognitivo e os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa de pesquisa, na parte superior do portal. Selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **Escreva o Nome do Grupo de Recursos**, introduza o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a API de Pesquisa de Notícias do Bing, veja [O que é a Pesquisa de Notícias do Bing?](index.yml).
