---
title: 'Início rápido: Verifique a ortografia com a API REST do Verificação Ortográfica do Bing eC#'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST do Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: be9301bdc70279974bbdbb5d2cb874e5bccc9358
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898449"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Início rápido: Verifique a ortografia com a API REST do Verificação Ortográfica do Bing eC#

Use este guia de início rápido para fazer sua primeira chamada para a API REST do Verificação Ortográfica do Bing. Esse aplicativo C# simples envia uma solicitação para a API e retorna uma lista de correções sugeridas. Apesar de esta aplicação estar escrita em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/).
* Para instalar o `Newtonsoft.Json` como um pacote NuGet no Visual Studio:
    1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no arquivo da solução.
    1. Selecione **gerenciar pacotes NuGet para solução**.
    1. Procure `Newtonsoft.Json` e instale o pacote.
* Se você estiver usando o linux/MacOS, esse aplicativo poderá ser executado usando o [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova solução de console chamada `SpellCheckSample` no Visual Studio. Em seguida, adicione os seguintes espaços de nomes ao ficheiro de código principal.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Crie variáveis para o ponto de extremidade da API, sua chave de assinatura e o texto a ser marcado para verificação ortográfica.

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

3. Crie uma variável para os parâmetros de pesquisa. Acrescente seu código de mercado após `mkt=`. O código do mercado é o país do qual você faz a solicitação. Além disso, acrescente seu modo de verificação ortográfica após `&mode=`. O modo é `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maior parte da ortografia, mas não a quantidade de erros gramaticais).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Criar e enviar uma solicitação de verificação ortográfica

1. Crie uma função assíncrona chamada `SpellCheck()` para enviar uma solicitação à API. Crie um `HttpClient`e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, execute as etapas a seguir na função.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Crie o URI para sua solicitação anexando o host, o caminho e os parâmetros.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Crie uma lista com um objeto `KeyValuePair` que contém o texto e use-o para criar um objeto `FormUrlEncodedContent`. Defina as informações de cabeçalho e use `PostAsync()` para enviar a solicitação.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Obter e imprimir a resposta da API

### <a name="get-the-client-id-header"></a>Obter o cabeçalho da ID do cliente

Se a resposta contiver um cabeçalho `X-MSEdge-ClientID`, obtenha o valor e imprima-o.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Obter a resposta

Obtenha a resposta da API. Desserializar o objeto JSON e imprimi-lo no console.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Chamar a função de verificação ortográfica

Na função principal do seu projeto, chame `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Exemplo de resposta JSON

O JSON devolve uma resposta de êxito, conforme apresentado no exemplo seguinte: 

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
> [Criar uma aplicação Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
