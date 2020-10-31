---
title: 'Quickstart: Obtenha insights de imagem utilizando a API REST e C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a fazer upload de uma imagem utilizando a API de Pesquisa Visual Bing e C#e, em seguida, obtenha insights sobre a imagem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: c39f1eeda5ccdfb72a58f5c082d14100812ec0fa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075036"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Quickstart: Obtenha insights de imagem utilizando a API e C de Pesquisa Visual Bing #

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Este quickstart demonstra como fazer o upload de uma imagem para a API de Pesquisa Visual Bing e ver as ideias que retorna.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* O [quadro Json.NET,](https://www.newtonsoft.com/json)disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, pode executar esta aplicação utilizando [o Mono.](https://www.mono-project.com/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. No Visual Studio, crie uma nova solução de consola chamada BingSearchApisQuickStart. Adicione os seguintes espaços de nome ao ficheiro de código principal:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adicione variáveis para a sua chave de subscrição, ponto final e caminho para a imagem que pretende carregar. Pelo `uriBase` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Crie um método nomeado `GetImageFileName()` para obter o caminho para a sua imagem.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Crie um método para obter os dados binários da imagem.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Construir os dados do formulário

1. Para fazer o upload de uma imagem local, primeiro construa os dados do formulário para enviar para a API. Os dados do formulário incluem o `Content-Disposition` cabeçalho, o `name` parâmetro definido para "imagem", e o `filename` parâmetro definido para o nome do ficheiro da imagem. O conteúdo do formulário contém os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. Adicione cordas de fronteira para formatar os dados do formulário POST. As cordas de fronteira determinam o início, fim e caracteres newline para os dados.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. Utilize as seguintes variáveis para adicionar parâmetros aos dados do formulário:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. Crie uma função nomeada `BuildFormDataStart()` para criar o início dos dados do formulário utilizando as cordas de fronteira e o caminho da imagem.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. Crie uma função nomeada `BuildFormDataEnd()` para criar o fim dos dados do formulário utilizando as cordas de fronteira.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Ligue para a API de Pesquisa Visual Bing

1. Crie uma função para ligar para o ponto final de Pesquisa Visual Bing e devolver a resposta JSON. A função toma o início e o fim dos dados do formulário, uma matriz byte contendo os dados de imagem, e um `contentType` valor.

2. Utilize um `WebRequest` para armazenar o seu URI, o valor de conteúdoType e os cabeçalhos.  

3. Use `request.GetRequestStream()` para escrever o seu formulário e dados de imagem, e depois obtenha a resposta. A sua função deve ser semelhante ao seguinte código:
        
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

## <a name="create-the-main-method"></a>Criar o método principal

1. No `Main()` método da sua aplicação, obtenha o nome de ficheiro e os dados binários da sua imagem.

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Configurar o corpo POST formando o seu limite. Em seguida, ligue `BuildFormDataStart()` e `BuildFormDataEnd()` crie os dados do formulário.

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Crie o `ContentType` valor formando `CONTENT_TYPE_HEADER_PARAMS` e o limite de dados do formulário.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Obtenha a resposta da API ligando `BingImageSearch()` e, em seguida, imprima a resposta.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Utilizar HttpClient

Se `HttpClient` utilizar, pode utilizar a `MultipartFormDataContent` classe para construir os dados do formulário. Utilize as seguintes secções de código para substituir os métodos correspondentes no exemplo anterior:

1. Substitua o método `Main()` pelo código abaixo:

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

2. Substitua o método `BingImageSearch()` pelo código abaixo:

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
> [Criar uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
