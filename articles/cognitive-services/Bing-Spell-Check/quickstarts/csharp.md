---
title: 'Quickstart: Verifique a ortografia com a REST API e C# - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Começar a usar a API bing spell check REST para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 036ea00362b604957a1887127fca0b8d775d4e7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382970"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Quickstart: Verifique a ortografia com o Bing Spell Check REST API e C #

Use este quickstart para fazer a sua primeira chamada para a API de Verificação de Feitiços de Bing. Este simples pedido de C# envia um pedido à API e devolve uma lista de correções sugeridas. Apesar de esta aplicação estar escrita em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou mais tarde.](https://www.visualstudio.com/downloads/)
* Para `Newtonsoft.Json` instalar como um pacote NuGet em estúdio Visual:
    1. No **Solution Explorer,** clique à direita no ficheiro Solução.
    1. Selecione **Gerir pacotes NuGet para solução**.
    1. Procure `Newtonsoft.Json` e instale o pacote.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada utilizando [o Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova `SpellCheckSample` solução de consola nomeada no Visual Studio. Em seguida, adicione os seguintes espaços de nomes ao ficheiro de código principal.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Crie variáveis para o ponto final da API, a sua chave de subscrição e o texto a ser verificado. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Crie uma variável para os seus parâmetros de pesquisa. Anexar o seu `mkt=`código de mercado depois de . O código de mercado é o país de onde faz o pedido. Além disso, apende `&mode=`o seu modo de verificação ortográfica depois de . O modo `proof` é ou (apanha a `spell` maioria dos erros ortográficos/gramaticais) ou (captura a maioria da ortografia, mas não tantos erros gramaticais).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Criar e enviar um pedido de verificação ortográfica

1. Crie uma função assíncrona chamada `SpellCheck()` para enviar um pedido à API. Crie `HttpClient`um e adicione a `Ocp-Apim-Subscription-Key` sua chave de subscrição ao cabeçalho. Em seguida, execute os seguintes passos dentro da função.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Crie o URI para o seu pedido, aparando o seu anfitrião, caminho e parâmetros.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Crie uma `KeyValuePair` lista com um objeto que contenha o seu texto e use-o para criar um `FormUrlEncodedContent` objeto. Detete a informação do cabeçalho e utilize `PostAsync()` para enviar o pedido.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Obtenha e imprima a resposta da API

### <a name="get-the-client-id-header"></a>Obtenha o cabeçalho de identificação do cliente

Se a resposta `X-MSEdge-ClientID` contiver um cabeçalho, obtenha o valor e imprima-o.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Obtenha a resposta

Obtenha a resposta da API. Desserialize o objeto JSON e imprima-o para a consola.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Ligue para a função de verificação de feitiços

Na função principal do `SpellCheck()`seu projeto, ligue .

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Executar a aplicação

Construa e gereno seu projeto. Se estiver a usar o Visual Studio, prima **F5** para desinserm o ficheiro.

## <a name="example-json-response"></a>Exemplo resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
