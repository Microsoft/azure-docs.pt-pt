---
title: 'Quickstart: Gere uma miniatura - REST, C #'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b17eca87a3c63757656cebd3990a169ddb29ad89
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681189"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-c"></a>Quickstart: Gere uma miniatura utilizando a API e C de Visão Computacional #

Neste arranque rápido, gera-se uma miniatura a partir de uma imagem utilizando a API DE DESCANSO DA Visão Computacional. Com o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), pode gerar uma miniatura de uma imagem. Especifica a altura e a largura, que podem ser diferentes da proporção da imagem de entrada. A Computer Vision utiliza uma cultura inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de cultura baseadas nessa região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e corda final de serviço, nomeada `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` e, respectivamente.

## <a name="create-and-run-the-sample-application"></a>Criar e executar a aplicação de exemplo

Para criar o exemplo no Visual Studio, siga os seguintes passos:

1. Crie uma nova solução do Visual Studio no Visual Studio, com o modelo de Aplicação da Consola Visual C#.
1. Instale o pacote NuGet Newtonsoft.Json.
    1. No menu, clique em **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Gerir Pacotes NuGet para Solução**.
    1. Clique no separador **Procurar** e, na caixa do tipo **Pesquisa** "Newtonsoft.Json".
    1. Selecione **Newtonsoft.Json** quando for apresentado e, em seguida, clique na caixa de verificação junto do nome do seu projeto, e em **Instalar**.
1. Execute o programa.
1. Na linha de comandos, introduza o caminho para uma imagem local.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and base endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");
        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // The GenerateThumbnail method endpoint
        static string uriBase = endpoint + "vision/v3.0/generateThumbnail";
        // Add an image to your bin/debug/netcoreappX.X folder, then add the image name (with extension), here
        static string imageFilePath = @"my-image-name";

        public static void Main()
        {

            MakeThumbNailRequest(imageFilePath).Wait();

            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets a thumbnail image from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to use to create the thumbnail image.</param>
        static async Task MakeThumbNailRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                // The width and height parameters specify a thumbnail that's 
                // 200 pixels wide and 150 pixels high.
                // The smartCropping parameter is set to true, to enable smart cropping.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Check the HTTP status code of the response. If successful, display
                // display the response and save the thumbnail.
                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data for the thumbnail from the response.
                    byte[] thumbnailImageData =
                        await response.Content.ReadAsByteArrayAsync();

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    // Note: This will overwrite an existing file of the same name.
                    string thumbnailFilePath =
                        imageFilePath.Insert(imageFilePath.Length - 4, "_thumb");
                    File.WriteAllBytes(thumbnailFilePath, thumbnailImageData);
                    Console.WriteLine("\nThumbnail written to: {0}", thumbnailFilePath);
                }
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Examinar a resposta

É devolvida uma resposta com êxito como dados binários, que representa os dados da imagem da miniatura. Se o pedido for realizado com êxito, a miniatura é guardada na mesma pasta que a imagem local, com o nome original com o sufixo "_thumb". Se o pedido falhar, a resposta contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

A aplicação de exemplo apresenta uma resposta de êxito na janela da consola, semelhante ao seguinte exemplo:

```console
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="next-steps"></a>Próximos passos

Explore uma aplicação básica do Windows que utilize a Imagem Digitalizada para realizar o reconhecimento ótico de carateres (OCR); criar miniaturas com recorte inteligente; além de detetar, categorizar, etiquetar e descrever funcionalidades visuais, incluindo rostos, numa imagem. Para experimentar rapidamente as API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [API de Imagem Digitalizada com C&#35; Tutorial](../Tutorials/CSharpTutorial.md)
