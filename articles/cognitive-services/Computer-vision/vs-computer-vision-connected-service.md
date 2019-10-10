---
title: Serviço conectado do Visual Studio-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conecte-se ao API da Pesquisa Visual Computacional de um aplicativo Web ASP.NET Core usando o recurso serviço conectado do Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: e4308f98b6e547acd4adfb62ab78c0517247d905
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177094"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Utilizar os Serviços Ligados no Visual Studio para estabelecer ligação à API de Imagem Digitalizada

Este artigo e os respetivos artigos complementares fornecem detalhes para utilizar a funcionalidade de Serviço Ligado do Visual Studio para a API de Imagem Digitalizada dos Serviços Cognitivos. A funcionalidade está disponível no Visual Studio 2017 15.7 e posterior, com a extensão de Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 versão 15,7 ou posterior com a carga de trabalho de **desenvolvimento da Web** instalada. [Transfira-a agora](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Adicionar suporte ao seu projeto para a API de Imagem Digitalizada dos Serviços Cognitivos

1. Crie um novo projeto Web do ASP.NET Core. Utilize o modelo de projeto Empty (Vazio). 

1. No **Solution Explorer** (Explorador de Soluções), selecione **Add** (Adicionar)  > **Connected Service** (Serviço Ligado).
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.

   ![Clique com o botão direito do mouse em um projeto do Visual Studio: Adicionar > serviço conectado](../media/vs-common/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **API de Imagem Digitalizada dos Serviços Cognitivos**.

   ![Menu de serviços conectados: analisar imagens... é descrito](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Se já tiver sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, será apresentada uma página com uma lista pendente com as suas subscrições.

   ![Janela de API da Pesquisa Visual Computacional com a lista suspensa de assinatura realçada](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um nome para a API de Imagem Digitalizada ou escolha a ligação de Edição para modificar o nome gerado automaticamente, selecione o grupo de recursos e o Escalão de Preço.

   ![Editar detalhes do serviço ligado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Siga a ligação para obter detalhes sobre os escalões de preço.

1. Selecione Add (Adicionar) para adicionar suporte para o Connected Service (Serviço Ligado).
   O Visual Studio modifica seu projeto para adicionar os pacotes NuGet, entradas de ficheiro de configuração e outras alterações para suportar uma ligação à API de Imagem Digitalizada. A Janela de Saída mostra o registo do que está a acontecer ao seu projeto. Deverá ver algo semelhante ao seguinte:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.1
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Utilizar a API de Imagem Digitalizada para detetar atributos de uma imagem

1. Adicione as seguintes instruções using a Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adicione um campo de configuração e um construtor que inicialize o campo de configuração na classe `Startup` para ativar a configuração no seu programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Na pasta wwwroot do seu projeto, adicione uma pasta de imagens e um ficheiro de imagem. Por exemplo, pode utilizar uma das imagens desta [página de API de Imagem Digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Clique com o botão direito do mouse em uma das imagens, salve no disco rígido local e, em Gerenciador de Soluções, clique com o botão direito do mouse na pasta imagens e escolha **adicionar** > **Item existente** para adicioná-lo ao seu projeto. O seu projeto deve ter um aspeto semelhante ao seguinte no Explorador de Soluções: 
  
   ![Captura de tela da exibição do Gerenciador de soluções com um arquivo de imagem selecionado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Clique com o botão direito do rato no ficheiro de imagem, selecione Properties (Propriedades) e, em seguida, selecione **Copy if newer** (Copiar se for mais recente). 

   ![janela Propriedades da imagem; Copiar para o diretório de saída definido como copiar se mais recente](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Substitua o método Configure (Configuração) pelo código seguinte para aceder à API de Imagem Digitalizada e testar uma imagem.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Este código constrói um pedido HTTP com o URI e a imagem como conteúdo binário para uma chamada à API REST de Imagem Digitalizada.

1. Adicione as funções de programa auxiliar GetImageAsByteArray e JsonPrettyPrint.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Execute a aplicação Web e veja o que a API de Imagem Digitalizada encontrou na sua imagem.

   ![Imagem e resultados formatados da API de Imagem Digitalizada](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Esta ação elimina o serviço cognitivo e os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:** , escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o API da Pesquisa Visual Computacional lendo a [documentação do API da pesquisa Visual computacional](Home.md).
