---
title: 'Quickstart: Procure vídeos utilizando a REST API e C# - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar pedidos de pesquisa de vídeo para a API Bing Video Search REST usando C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 10/22/2020
ms.author: clschott
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f28ab0d81daaedeec83994fcebc3eb430023bbc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315843"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Quickstart: Procure vídeos utilizando a API e C de pesquisa de vídeo Bing #

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Vídeo Bing. Esta simples aplicação C# envia uma consulta de pesquisa de vídeo HTTP para a API e exibe a resposta JSON. Embora esta aplicação esteja escrita em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

O código-fonte desta amostra está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) com tratamento adicional de erros, funcionalidades e anotações de código.

## <a name="prerequisites"></a>Pré-requisitos

Terá de configurar a sua máquina para executar o núcleo .NET. Pode encontrar as instruções de instalação na página [.NET Core Downloads.](https://dotnet.microsoft.com/download) Pode executar esta aplicação no Windows, Linux, macOS ou num recipiente Docker. Terá de instalar o seu editor de código favorito. As descrições abaixo utilizam [o Visual Studio Code,](https://code.visualstudio.com/)que é um editor de plataforma cross- de código aberto. No entanto, pode usar todas as ferramentas com as quais se sinta confortável.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

O primeiro passo é criar uma nova aplicação. Abra um pedido de comando e crie um novo diretório para a sua aplicação. Que seja o diretório atual. Introduza o seguinte comando numa janela da consola:

```dotnetcli
dotnet new console --name VideoSearchClient
```

Terá de adicionar a seguinte diretiva no topo do seu método Principal para `using` que o compilador C# reconheça os tipos de Tarefa e JSON:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
```

Adicione variáveis para a sua chave de subscrição, ponto final e termo de pesquisa. Pelo `uriBase` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [personalizado subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

```csharp
// Replace the accessKey string value with your valid access key.
const string _accessKey = "enter your key here";

// Or use the custom subdomain endpoint displayed in the Azure portal for your resource.
const string _uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";

const string _searchTerm = "kittens";
```

Em seguida, atualize o método Principal para que possamos usar métodos Async. Adicione o modificador assínc e altere o tipo de retorno para Task.

```csharp
static async Task Main(string[] args)
{
    
}
```

Agora, tens um programa que não faz nada a não ser faz assíncronos. Vamos melhorá-lo.

## <a name="create-a-data-structure-to-hold-the-bing-video-search-api-response"></a>Criar uma estrutura de dados para manter a resposta da API de pesquisa de vídeo Bing

Defina uma `SearchResult` classe para conter os `Video` resultados da pesquisa de vídeo. Pode adicionar mais propriedades mais tarde quando precisar de outros campos a partir do resultado do JSON.

```cscharp
class SearchResult
{
    [JsonPropertyName("totalEstimatedMatches")]
    public int TotalEstimatedMatches { get; set; }

    [JsonPropertyName("value")]
    public List<Video> Videos { get; set; }
}

class Video
{
    [JsonPropertyName("name")]
    public string Name { get; set; }

    [JsonPropertyName("description")]
    public string Description { get; set; }

    [JsonPropertyName("thumbnailUrl")]
    public string ThumbnailUrl { get; set; }

    [JsonPropertyName("contentUrl")]
    public string ContentUrl { get; set; }
}
```

## <a name="create-and-handle-a-video-search-request"></a>Criar e lidar com um pedido de pesquisa de vídeo

Usamos `HttpClient` para fazer a chamada para a API. Primeiro, temos de adicionar o cabeçalho `Ocp-Apim-Subscription-Key` e a chave de acesso. 

```csharp
using var client = new HttpClient();
client.BaseAddress = new Uri(_uriBase);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", _accessKey);
```

Construa o URI do pedido de pesquisa. Formatar o termo de pesquisa `_searchTerm` antes de apdicê-lo à cadeia.

```csharp
var response = await client.GetAsync($"?q={Uri.EscapeDataString(_searchTerm)}");
```

## <a name="process-the-result"></a>Processar o resultado

Quando a resposta foi bem sucedida, podemos processar os dados do JSON. Deserizamos a corda JSON na nossa `SearchResult` que tínhamos criado antes. Loop para o resultado (se houver) e imprima o resultado na consola.

```csharp
if (response.IsSuccessStatusCode)
{
    var json = await response.Content.ReadAsStringAsync();
    var result = JsonSerializer.Deserialize<SearchResult>(json);

    foreach (var video in result.Videos)
    {
        Console.WriteLine($"Name: {video.Name}");
        Console.WriteLine($"ContentUrl: {video.ContentUrl}");
        Console.WriteLine();
    }
}
```

## <a name="example-json-response"></a>Exemplo JSON resposta 

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

 [O que é a API de Pesquisa de Vídeos do Bing?](../overview.md)
