---
title: 'Tutorial: API faceC#'
titleSuffix: Azure Cognitive Services
description: Crie uma aplicação do Windows simples que utilize a API Face dos Serviços Cognitivos para detetar características de rostos numa imagem.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: aafa115ee1e270b903e958e5b43826dfb4ff8bdb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435234"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Ligar à API Face dos Serviços Cognitivos ao utilizar os Serviços Ligados no Visual Studio

Ao utilizar a API Face dos Serviços Cognitivos, pode detetar, analisar, organizar e identificar rostos em fotografias.

Este artigo e os respetivos artigos complementares fornecem detalhes para utilizar a funcionalidade Serviço Ligado do Visual Studio para a API Face dos Serviços Cognitivos. A funcionalidade está disponível no Visual Studio 2017 15.7 e posterior, com a extensão de Serviços Cognitivos instalada.

## <a name="prerequisites"></a>Pré-requisitos

- **Uma subscrição do Azure**. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 versão 15.7** com a carga de trabalho de **Desenvolvimento Web** instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Criar um projeto e adicionar suporte para a API Face dos Serviços Cognitivos

1. Crie um novo projeto Web do ASP.NET Core. Utilize o modelo de projeto Empty (Vazio). 

1. No **Solution Explorer** (Explorador de Soluções), selecione **Add** (Adicionar)  > **Connected Service** (Serviço Ligado).
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.

   ![Adicionar item de menu do Connected Service (Serviço Ligado)](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. No menu de serviços disponíveis, selecione **Cognitive Services Face API** (API Face dos Serviços Cognitivos).

   ![Selecionar o serviço ao qual se pretende ligar](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Se já tiver sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, será apresentada uma página com uma lista pendente com as suas subscrições.

   ![Selecione a sua subscrição](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, selecione um nome para a API Face ou selecione a ligação Edit (Editar) para modificar o nome gerado automaticamente, selecione o grupo de recursos e o Pricing Tier (Escalão de Preço).

   ![Editar detalhes do serviço ligado](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Siga a ligação para obter detalhes sobre os escalões de preço.

1. Selecione Add (Adicionar) para adicionar suporte para o Connected Service (Serviço Ligado).
   O Visual Studio modifica o seu projeto para adicionar os pacotes NuGet, entradas de ficheiro de configuração e outras alterações para suportar uma ligação à API Face.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Utilizar a API Face para detetar características de rostos numa imagem

1. Adicione as seguintes instruções using a Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adicione um campo de configuração e um construtor que inicialize o campo de configuração na classe Startup para ativar a Configuração no seu programa.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Na pasta wwwroot do seu projeto, adicione uma pasta de imagens e um ficheiro de imagem. Por exemplo, pode utilizar uma das imagens desta [página de API Face](https://azure.microsoft.com/services/cognitive-services/face/). Clique com o botão direito do rato em uma das imagens, guardar para o disco rígido local, em seguida, no Explorador de soluções, clique com o botão direito na pasta de imagens e escolha **Add** > **Item existente** para adicioná-lo ao seu projeto. O seu projeto deve ter um aspeto semelhante ao seguinte no Explorador de Soluções:
 
   ![pasta de imagens com o ficheiro de imagem](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Clique com o botão direito do rato no ficheiro de imagem, selecione Properties (Propriedades) e, em seguida, selecione **Copy if newer** (Copiar se for mais recente).

   ![Copy if newer (Copiar se for mais recente)](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Substitua o método Configure (Configuração) pelo código seguinte para aceder à API Face e testar uma imagem. Altere a cadeia imagePath para o caminho correto e o nome de ficheiro para a sua imagem de rosto.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    O código neste passo constrói um pedido HTTP com uma chamada para a API REST Face através da chave que adicionou quando adicionou o serviço ligado.

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

1. Execute a aplicação Web e veja o que a API Face encontrou na imagem.
 
   ![Imagem e resultados formatados da API Face](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Esta ação elimina o serviço cognitivo e os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste Início Rápido nos resultados da pesquisa, selecione-o.
1. Selecione **Eliminar grupo de recursos**.
1. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Obtenha mais informações sobre a API Face ao ler a [Documentação da API Face](Overview.md).
