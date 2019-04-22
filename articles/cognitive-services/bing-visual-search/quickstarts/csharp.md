---
title: 'Início rápido: Obtenha informações de imagem com a API de REST de pesquisa Visual do Bing eC#'
titleSuffix: Azure Cognitive Services
description: Aprenda a carregar uma imagem para a API de pesquisa Visual do Bing e obter conhecimentos sobre ele.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 3/28/2019
ms.author: scottwhi
ms.openlocfilehash: d2f5e87bd6c6780e8504abe1753e90eca5db763a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880411"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Início rápido: Obtenha informações de imagem com a API de REST de pesquisa Visual do Bing eC#

Este início rápido demonstra como carregar uma imagem para a API de pesquisa Visual do Bing e para ver as informações que ele retorna.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* O [Json.NET framework](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, pode executar esta aplicação a utilizar [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. No Visual Studio, crie uma nova solução de consola com o nome BingSearchApisQuickStart. Adicione os seguintes espaços de nomes para o ficheiro de código principal:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adicione as variáveis para a sua chave de subscrição, o ponto final e o caminho para a imagem que pretende carregar:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Crie um método chamado `GetImageFileName()` para obter o caminho para a sua imagem:
    
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

Para carregar uma imagem do local, crie primeiro os dados do formulário para enviar para a API. Os dados do formulário tem de incluir o `Content-Disposition` cabeçalho, seu `name` parâmetro tem de ser definido como "imagem" e o `filename` parâmetro pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário contém os dados binários da imagem. O tamanho da imagem máximo que pode carregar é de 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Acrescentar cadeias de caracteres de limites para formatar os dados de formulário POST. Cadeias de caracteres de limite determinam os caracteres de início, fim e de nova linha para os dados:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Utilize as seguintes variáveis para adicionar parâmetros para os dados do formulário:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Criar uma função chamada `BuildFormDataStart()` para criar o início dos dados de formulário usando as cadeias de caracteres de limite e o caminho da imagem:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Criar uma função chamada `BuildFormDataEnd()` para criar o fim dos dados de formulário com as cadeias de caracteres de limites:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Chamar a API de pesquisa Visual do Bing

1. Crie uma função para chamar o ponto de extremidade de pesquisa Visual do Bing e retornar a resposta JSON. A função usa o início e de fim dos dados de formulário, uma matriz de bytes que contém os dados de imagem e um `contentType` valor.

2. Utilize um `WebRequest` para armazenar seu URI, o valor de contentType e cabeçalhos.  

3. Utilize `request.GetRequestStream()` para escrever os dados do formulário e imagem, em seguida, obtenha a resposta. A função deve ser semelhante à abaixo:
        
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

1. Na `Main` método do seu aplicativo, obter os dados de nome de ficheiro e o binário de sua imagem:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Configure o corpo do POST, formatação do limite para o mesmo. Em seguida, chame `startFormData()` e `endFormData` para criar os dados do formulário:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Criar a `ContentType` valor por formatação `CONTENT_TYPE_HEADER_PARAMS` e o limite de dados do formulário:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Obtenha a resposta de API chamando `BingImageSearch()` e imprimir a resposta:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Utilizar HttpClient

Se usar `HttpClient`, pode utilizar o `MultipartFormDataContent` classe para criar os dados do formulário. Apenas utilize as secções seguintes de código para substituir os métodos correspondentes no exemplo anterior.

Substitua o `Main` método com esse código:

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

Substitua o `BingImageSearch` método com esse código:

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação de web de página única de pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
