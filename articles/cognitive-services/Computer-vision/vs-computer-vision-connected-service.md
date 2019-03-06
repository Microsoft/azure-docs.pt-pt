---
title: Ligado do Visual Studio Service - de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Ligar-se para a API de imagem digitalizada a partir de uma aplicação de web do ASP.NET Core com a funcionalidade de serviço ligado do Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 6a576f2817069d7095ea863198168be083d0c6b5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450951"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Utilizar os Serviços Ligados no Visual Studio para estabelecer ligação à API de Imagem Digitalizada

Ao utilizar a API de Imagem Digitalizada dos Serviços Cognitivos pode extrair informações detalhadas para categorizar e processar dados do visual e para realizar moderação assistida por computador de imagens para ajudar a organizar os seus serviços.

Este artigo e os respetivos artigos complementares fornecem detalhes para utilizar a funcionalidade de Serviço Ligado do Visual Studio para a API de Imagem Digitalizada dos Serviços Cognitivos. A funcionalidade está disponível no Visual Studio 2017 15.7 e posterior, com a extensão de Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- **Uma subscrição do Azure**. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 versão 15.7** com a carga de trabalho de **Desenvolvimento Web** instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Adicionar suporte ao seu projeto para a API de Imagem Digitalizada dos Serviços Cognitivos

1. Crie um novo projeto Web do ASP.NET Core. Utilize o modelo de projeto Empty (Vazio). 

1. No **Solution Explorer** (Explorador de Soluções), selecione **Add** (Adicionar)  > **Connected Service** (Serviço Ligado).
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.

   ![Captura de ecrã do menu de contexto num projeto do Visual Studio: Adicionar > serviço ligado](../media/vs-common/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, escolha **API de Imagem Digitalizada dos Serviços Cognitivos**.

   ![o menu de serviços ligados, realce analisar imagens com imagem digitalizada](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Se já tiver sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, será apresentada uma página com uma lista pendente com as suas subscrições.

   ![Uma janela de "API de imagem digitalizada" Visual Studio com a lista pendente de subscrição realçada](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um nome para a API de Imagem Digitalizada ou escolha a ligação de Edição para modificar o nome gerado automaticamente, selecione o grupo de recursos e o Escalão de Preço.

   ![Editar detalhes do serviço ligado](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Siga a ligação para obter detalhes sobre os escalões de preço.

1. Escolha Adicionar para adicionar suporte para o Serviço Ligado.
   O Visual Studio modifica seu projeto para adicionar os pacotes NuGet, entradas de ficheiro de configuração e outras alterações para suportar uma ligação à API de Imagem Digitalizada. A Janela de Saída mostra o registo do que está a acontecer ao seu projeto. Deverá ver algo semelhante ao seguinte:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Utilizar a API de Imagem Digitalizada para detetar atributos de uma imagem

1. Adicione as instruções de utilização seguintes a Startup.cs.
 
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

1. Na pasta wwwroot do seu projeto, adicione uma pasta de imagens e um ficheiro de imagem. Por exemplo, pode utilizar uma das imagens desta [página de API de Imagem Digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Com o botão direito em uma das imagens, guardar para o disco rígido local, em seguida, no Explorador de soluções, clique com o botão direito na pasta de imagens e escolha **Add** > **Item existente** para adicioná-lo ao seu projeto. O seu projeto deve ter um aspeto semelhante ao seguinte no Explorador de Soluções: 
  
   ![Captura de ecrã da vista do Explorador de soluções com um ficheiro de imagem selecionada](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Clique com o botão direito do rato no ficheiro de imagem, selecione Properties (Propriedades) e, em seguida, selecione **Copy if newer** (Copiar se for mais recente). 

   ![Uma janela de propriedades de imagem; Copiar para diretório de saída está definido para copiar se for mais recente](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
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

1. Adicione as funções dos auxiliares GetImageAsByteArray e JsonPrettyPrint.

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

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste Início Rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a API de Imagem Digitalizada lendo a [Documentação da API de Imagem Digitalizada](Home.md).
