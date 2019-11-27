---
title: 'Início rápido: obter informações de imagem usando a API REST e C# -pesquisa visual do Bing'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem no API da Pesquisa Visual do Bing e obter informações sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: 82c1159aca51bc30839f5380a414bd2b3b488bb8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383640"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Início rápido: obter informações de imagem usando a API REST do Pesquisa Visual do Bing eC#

Este guia de início rápido demonstra como carregar uma imagem no API da Pesquisa Visual do Bing e exibir as informações que ele retorna.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* A [estrutura JSON.net](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se você estiver usando o linux/MacOS, poderá executar esse aplicativo usando o [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. No Visual Studio, crie uma nova solução de console chamada BingSearchApisQuickStart. Adicione os seguintes namespaces ao arquivo de código principal:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adicione variáveis para sua chave de assinatura, ponto de extremidade e caminho para a imagem que você deseja carregar:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Crie um método chamado `GetImageFileName()` para obter o caminho para a imagem:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Crie um método para obter os dados binários da imagem:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Criar os dados do formulário

Para carregar uma imagem local, primeiro você cria os dados do formulário a serem enviados para a API. Os dados do formulário devem incluir o cabeçalho `Content-Disposition`, seu parâmetro `name` deve ser definido como "Image", e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário contém os dados binários da imagem. O tamanho máximo da imagem que você pode carregar é 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Adicione cadeias de caracteres de limite para formatar os dados do formulário de POSTAgem. Cadeias de caracteres de limite determinam o início, o fim e o caractere de nova linha para os dados:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Use as seguintes variáveis para adicionar parâmetros aos dados do formulário:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Crie uma função chamada `BuildFormDataStart()` para criar o início dos dados do formulário usando as cadeias de caracteres de limite e o caminho da imagem:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Crie uma função chamada `BuildFormDataEnd()` para criar o final dos dados do formulário usando as cadeias de caracteres de limite:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Chamar o API da Pesquisa Visual do Bing

1. Crie uma função para chamar o ponto de extremidade Pesquisa Visual do Bing e retornar a resposta JSON. A função usa o início e o final dos dados do formulário, uma matriz de bytes que contém os dados da imagem e um valor `contentType`.

2. Use um `WebRequest` para armazenar seu URI, valor contentType e cabeçalhos.  

3. Use `request.GetRequestStream()` para gravar seus dados de formulário e imagem e obter a resposta. Sua função deve ser semelhante à seguinte:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Criar o método Main

1. No método `Main` do seu aplicativo, obtenha o nome de arquivo e os dados binários da imagem:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Configure o corpo da POSTAgem Formatando o limite para ele. Em seguida, chame `startFormData()` e `endFormData` para criar os dados do formulário:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Crie o valor `ContentType` Formatando `CONTENT_TYPE_HEADER_PARAMS` e o limite de dados do formulário:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Obtenha a resposta da API chamando `BingImageSearch()` e imprima a resposta:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Utilizar HttpClient

Se você usar `HttpClient`, poderá usar a classe `MultipartFormDataContent` para criar os dados do formulário. Basta usar as seções de código a seguir para substituir os métodos correspondentes no exemplo anterior.

Substitua o método `Main` por este código:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Substitua o método `BingImageSearch` por este código:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única Pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
