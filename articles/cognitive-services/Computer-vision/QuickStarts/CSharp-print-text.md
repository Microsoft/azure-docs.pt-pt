---
title: 'Início rápido: Extrair texto impresso (OCR) - REST,C#'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá extrair texto impresso de uma imagem através da API de Imagem Digitalizada com o C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 18a00c2aebd05d2ed2da54e436d245c70bf25051
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604465"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-c"></a>Início rápido: Extrair texto impresso (OCR) com a API de REST de visão do computador eC#

Neste início rápido, irá extrair o texto impresso com reconhecimento ótico de carateres (OCR) de uma imagem com API de REST do imagem digitalizada. Com o [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) funcionalidade, pode detetar texto impresso numa imagem e extrair os carateres reconhecidos para um fluxo de carateres máquina utilizável.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Pode obter uma chave de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a imagem digitalizada e obtenha a chave.

## <a name="create-and-run-the-sample-application"></a>Criar e executar a aplicação de exemplo

Para criar o exemplo no Visual Studio, siga os seguintes passos:

1. Crie uma nova solução do Visual Studio no Visual Studio, com o modelo de Aplicação da Consola Visual C#.
1. Instale o pacote NuGet Newtonsoft.Json.
    1. No menu, clique em **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Gerir Pacotes NuGet para Solução**.
    1. Clique no separador **Procurar** e, na caixa do tipo **Pesquisa** "Newtonsoft.Json".
    1. Selecione **Newtonsoft.Json** quando for apresentado e, em seguida, clique na caixa de verificação junto do nome do seu projeto, e em **Instalar**.
1. Substitua o código em `Program.cs` pelo código seguinte e faça as seguintes alterações ao código onde for necessário:
    1. Substitua o valor de `subscriptionKey` pela chave de subscrição.
    1. Substitua o valor de `uriBase` pelo URL de ponto final do método [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) da região do Azure onde obteve as chaves de subscrição, se necessário.
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
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. 
                // The language parameter doesn't specify a language, so the 
                // method detects it automatically.
                // The detectOrientation parameter is set to true, so the method detects and
                // and corrects text orientation before detecting text.
                string requestParameters = "language=unk&detectOrientation=true";

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

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
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

O JSON devolve uma resposta de êxito. A aplicação de exemplo analisa e apresenta uma resposta de êxito na janela da consola, semelhante ao seguinte exemplo:

```json
{
    "language": "en",
    "textAngle": -1.5000000000000335,
    "orientation": "Up",
    "regions": [
        {
            "boundingBox": "154,49,351,575",
            "lines": [
                {
                    "boundingBox": "165,49,340,117",
                    "words": [
                        {
                            "boundingBox": "165,49,63,109",
                            "text": "A"
                        },
                        {
                            "boundingBox": "261,50,244,116",
                            "text": "GOAL"
                        }
                    ]
                },
                {
                    "boundingBox": "165,169,339,93",
                    "words": [
                        {
                            "boundingBox": "165,169,339,93",
                            "text": "WITHOUT"
                        }
                    ]
                },
                {
                    "boundingBox": "159,264,342,117",
                    "words": [
                        {
                            "boundingBox": "159,264,64,110",
                            "text": "A"
                        },
                        {
                            "boundingBox": "255,266,246,115",
                            "text": "PLAN"
                        }
                    ]
                },
                {
                    "boundingBox": "161,384,338,119",
                    "words": [
                        {
                            "boundingBox": "161,384,86,113",
                            "text": "IS"
                        },
                        {
                            "boundingBox": "274,387,225,116",
                            "text": "JUST"
                        }
                    ]
                },
                {
                    "boundingBox": "154,506,341,118",
                    "words": [
                        {
                            "boundingBox": "154,506,62,111",
                            "text": "A"
                        },
                        {
                            "boundingBox": "248,508,247,116",
                            "text": "WISH"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Explore uma aplicação básica do Windows que utilize a Imagem Digitalizada para realizar o reconhecimento ótico de carateres (OCR); criar miniaturas com recorte inteligente; além de detetar, categorizar, etiquetar e descrever funcionalidades visuais, incluindo rostos, numa imagem.

> [!div class="nextstepaction"]
> [API de imagem digitalizada C# Tutorial](../Tutorials/CSharpTutorial.md)
