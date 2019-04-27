---
title: 'Tutorial: Ligar ao serviço de análise de texto com os serviços ligados no Visual Studio'
titleSuffix: Azure Cognitive Services
description: Saiba como ligar à análise de texto a partir de uma aplicação web ASP.NET Core.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4e1c03085d6b1d0099ac66dd3d1dadd981a561aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829263"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Tutorial: Ligar ao serviço de análise de texto com os serviços ligados no Visual Studio

Ao utilizar o Serviço de Análise de Texto, pode extrair informações detalhadas para categorizar e processar dados do elemento visual e para realizar moderação assistida por computador de imagens para ajudar a organizar os seus serviços.

Este artigo e os respetivos artigos complementares fornecem detalhes para utilizar a funcionalidade de Serviço Ligado do Visual Studio para o Serviço de Análise de Texto. A funcionalidade está disponível no Visual Studio 2017 15.7 e posterior, com a extensão de Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versão 15.7, com a carga de trabalho de Desenvolvimento Web instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Adicionar suporte ao seu projeto para o Serviço de Análise de Texto

1. Crie um novo projeto Web do ASP.NET Core chamado TextAnalyticsDemo. Utilize o modelo de projeto Aplicação Web (Model-View-Controller), com todas as predefinições. É importante atribuir o nome MyWebApplication ao projeto, para que o espaço de nomes corresponda ao copiar código para o projeto.  O exemplo nestes artigos utiliza o MVC, mas pode utilizar o Serviço Ligado de Análise de Texto com qualquer tipo de projeto do ASP.NET.

1. No **Explorador de Soluções**, faça duplo clique no item **Connected Service** (Serviço Ligado).
   É apresentada a página Connected Service (Serviço Ligado), com os serviços que pode adicionar ao seu projeto.

   ![Captura de ecrã a mostrar o Connected Service (Serviço Ligado) no Explorador de Soluções](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. No menu de serviços disponíveis, selecione **Avaliar Sentimentos com a Análise de Texto**.

   ![Captura do ecrã Connected Services (Serviços Ligados)](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Se já tiver sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, será apresentada uma página com uma lista pendente com as suas subscrições.

   ![Captura do ecrã Connected Service (Serviço Ligado) da Análise de Texto](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um nome para o Serviço de Análise de Texto ou escolha a ligação **Edit** (Edição) para modificar o nome gerado automaticamente, selecione o grupo de recursos e o Escalão de Preço.

   ![Captura de ecrã a mostrar os campos de grupo de recursos e escalão de preço](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Siga a ligação para obter detalhes sobre os escalões de preço.

1. Selecione **Add** (Adicionar) para adicionar suporte para o Serviço Ligado.
   O Visual Studio modifica o seu projeto para adicionar os pacotes NuGet, entradas de ficheiro de configuração e outras alterações para suportar uma ligação ao Serviço de Análise de Texto. A **Janela de Saída** mostra o registo do que está a acontecer ao seu projeto. Deverá ver algo semelhante ao seguinte:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Utilize o Serviço de Análise de Texto para detetar o idioma para um exemplo de texto.

1. Adicione as seguintes instruções using a Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Adicione um campo de configuração e um construtor que inicialize o campo de configuração na classe Startup para ativar a Configuração no seu programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Adicione um ficheiro de classe na pasta Controllers denominada DemoTextAnalyzeController e substitua os respetivos conteúdos pelo seguinte código:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    O código inclui GetTextAnalyzeClient para obter o objeto de cliente que pode utilizar para chamar a API de Análise de Texto e um processador de pedidos que chama DetectLanguage num determinado texto.

1. Adicione a classe de programa auxiliar de MyHandler que é utilizada pelo código anterior.

    ```csharp
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

1. Na pasta Modelos, adicione uma classe para o modelo.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Adicione uma Vista para mostrar o texto analisado, o idioma determinado e a pontuação que representa o nível de confiança na análise. Para tal, clique com o botão direito do rato na pasta **Views**, selecione **Add** (Adicionar) e, em seguida, **View** (Vista). Na caixa de diálogo apresentada, forneça um nome _TextAnalyzeResult_, aceite as predefinições para adicionar um novo ficheiro chamado _TextAnalyzeResult.cshtml_ na pasta **Views** e copie os seguintes conteúdos para a mesma:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Compile e execute o exemplo localmente. Introduza algum texto e veja que idioma é detetado pela análise de texto.
   
## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Esta ação elimina o serviço cognitivo e os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste tutorial nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Serviço de Análise de Texto com a leitura da [Documentação do Serviço de Análise de Texto](index.yml).
