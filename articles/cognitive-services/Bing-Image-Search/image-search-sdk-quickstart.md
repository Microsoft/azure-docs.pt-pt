---
title: 'Pedido de início rápido: E filtrar imagens com o SDK com c#'
description: Neste início rápido, pedir e filtrar as imagens devolvidas pela pesquisa de imagens Bing, usando a linguagem c#.
titleSuffix: Azure cognitive services setup Image search SDK C# console application
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 01/29/2018
ms.author: v-gedod
ms.openlocfilehash: 81375019e53b49b531fde1f81fbcb9a061cc5562
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987604"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-c"></a>Pedido de início rápido: E filtrar imagens com o SDK e c#

O SDK de pesquisa de imagens do Bing contém a funcionalidade da API REST para solicitações de imagem e os resultados da análise. 

O [da origem de código para exemplos de c# SDK do Bing imagens pesquisa](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) está disponível no Hub de Git.

## <a name="application-dependencies"></a>Dependências de aplicações

Para configurar uma aplicação de consola com o SDK de pesquisa de imagens do Bing, navegue para o `Manage NuGet Packages` opção a partir do Explorador de soluções no Visual Studio.  Adicionar o `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pacote.

Instalar o [pacote de pesquisa de imagens do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0) também instala as dependências, incluindo:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft

## <a name="image-search-client"></a>Cliente de pesquisa de imagem
Para criar uma instância do `ImageSearchAPI` cliente, adicionar com diretivas:
```
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;

```
Em seguida, criar uma instância do cliente:
```
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Utilize o cliente para pesquisar com um texto de consulta:
```
// Search for "Yosemite National Park"
var imageResults = client.Images.SearchAsync(query: "Canadian Rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");

```
Analisar os resultados de imagem devolvidos pela consulta anterior:

```
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();

    Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
    Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");
    Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
    Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
}
else
{
    Console.WriteLine("Couldn't find image results!");
}

Console.WriteLine($"\r\nImage result total estimated matches: {imageResults.TotalEstimatedMatches}");

```

## <a name="complete-console-application"></a>Aplicação de consola concluída

A aplicação de consola seguinte executa a consulta de previamente definida "Rockies canadense" para obter os resultados de pesquisa, em seguida, imprimir primeira imagem em miniatura, token url de informações e o url de conteúdo de imagem:

```
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
using System.Linq;

namespace ImageSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
            ImageResults(client);
            ImageDetail(client);
            ImageTrending(client);
            ImageSearchWithFilters(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

        // Searches for results on query (Canadian Rockies) and prints first image insights token, thumbnail url, and image content url.
        static void ImageResults(ImageSearchAPI client)
        {
            try
            {
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image results!");
                    }

                    Console.WriteLine($"\r\nImage result total estimated matches: {imageResults.TotalEstimatedMatches}");
                    Console.WriteLine($"Image result next offset: {imageResults.NextOffset}");

                    // Pivot suggestions
                    if (imageResults.PivotSuggestions.Count > 0)
                    {
                        var firstPivot = imageResults.PivotSuggestions.First();

                        Console.WriteLine($"\r\nPivot suggestion count: {imageResults.PivotSuggestions.Count}");
                        Console.WriteLine($"First pivot: {firstPivot.Pivot}");

                        if (firstPivot.Suggestions.Count > 0)
                        {
                            var firstSuggestion = firstPivot.Suggestions.First();

                            Console.WriteLine($"\r\nSuggestion count: {firstPivot.Suggestions.Count}");
                            Console.WriteLine($"First suggestion text: {firstSuggestion.Text}");
                            Console.WriteLine($"First suggestion web search url: {firstSuggestion.WebSearchUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find suggestions!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find pivot suggestions!");
                    }

                    // Query expansions
                    if (imageResults.QueryExpansions.Count > 0)
                    {
                        var firstQueryExpansion = imageResults.QueryExpansions.First();

                        Console.WriteLine($"\r\nQuery expansion count: {imageResults.QueryExpansions.Count}");
                        Console.WriteLine($"First query expansion text: {firstQueryExpansion.Text}");
                        Console.WriteLine($"First query expansion search link: {firstQueryExpansion.SearchLink}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find query expansions!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }
        }
    }
}

```

## <a name="search-options"></a>Opções de pesquisa

Os exemplos de pesquisa do Bing demonstram vários recursos do SDK.  Adicione as seguintes funções para definido anteriormente `ImageSrchSDK` classe.

### <a name="search-using-a-filter"></a>Utilizar um filtro de pesquisa

Imagens de pesquisa para "studio ghibli", filtrada para os GIF animados e amplo aspecto, em seguida, verifique se o número de resultados e imprimir insightsToken, url de miniaturas e url do primeiro resultado.

```
        public static void ImageSearchWithFilters(ImageSearchAPI client)
        {
            try
            {
                var imageResults = client.Images.SearchAsync(query: "studio ghibli", imageType: ImageType.AnimatedGif, aspect: ImageAspect.Wide).Result;
                Console.WriteLine("Search images for \"studio ghibli\" results that are animated gifs and wide aspect");

                if (imageResults == null)
                {
                    Console.WriteLine("Didn't see any image result data.");
                }
                else
                {
                    // First image result
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        Console.WriteLine($"First image insightsToken: {firstImageResult.ImageInsightsToken}");
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image web search url: {firstImageResult.WebSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }

```

### <a name="trending-images"></a>Imagens populares

Procurar imagens populares e, em seguida, certifique-se de categorias e mosaicos.

```
        public static void ImageTrending(ImageSearchAPI client)
        {
            try
            {
                var trendingResults = client.Images.TrendingAsync().Result;
                Console.WriteLine("Search trending images");

                if (trendingResults == null)
                {
                    Console.WriteLine("Didn't see any trending image data.");
                }
                else
                {
                    // Categories
                    if (trendingResults.Categories?.Count > 0)
                    {
                        var firstCategory = trendingResults.Categories[0];
                        Console.WriteLine($"\r\nCategory count: {trendingResults.Categories.Count}");
                        Console.WriteLine($"First category title: {firstCategory.Title}");

                        // Tiles
                        if (firstCategory.Tiles?.Count > 0)
                        {
                            var firstTile = firstCategory.Tiles[0];
                            Console.WriteLine($"\r\nTile count: {firstCategory.Tiles.Count}");
                            Console.WriteLine($"First tile text: {firstTile.Query.Text}");
                            Console.WriteLine($"First tile url: {firstTile.Query.WebSearchUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find tiles!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find categories!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }

```

### <a name="image-details"></a>Detalhes da imagem

Procure imagens "Degas" e, em seguida, procure os detalhes da imagem da primeira imagem.
```
        public static void ImageDetail(ImageSearchAPI client)
        {

            try
            {
                var imageResults = client.Images.SearchAsync(query: "degas").Result;

                var firstImage = imageResults?.Value?.FirstOrDefault();

                if (firstImage != null)
                {
                    var modules = new List<string>() { ImageInsightModule.All };
                    var imageDetail = client.Images.DetailsAsync(query: "degas", insightsToken: firstImage.ImageInsightsToken, modules: modules).Result;
                    Console.WriteLine($"\r\nSearch detail for image insightsToken={firstImage.ImageInsightsToken}");

                    if (imageDetail != null)
                    {
                        // Insights token
                        Console.WriteLine($"Expected image insights token: {imageDetail.ImageInsightsToken}");

                        // Best representative query
                        if (imageDetail.BestRepresentativeQuery != null)
                        {
                            Console.WriteLine($"\r\nBest representative query text: {imageDetail.BestRepresentativeQuery.Text}");
                            Console.WriteLine($"Best representative query web search url: {imageDetail.BestRepresentativeQuery.WebSearchUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find best representative query!");
                        }

                        // Caption 
                        if (imageDetail.ImageCaption != null)
                        {
                            Console.WriteLine($"\r\nImage caption: {imageDetail.ImageCaption.Caption}");
                            Console.WriteLine($"Image caption data source url: {imageDetail.ImageCaption.DataSourceUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image caption!");
                        }

                        // Pages including the image
                        if (imageDetail.PagesIncluding?.Value?.Count > 0)
                        {
                            var firstPage = imageDetail.PagesIncluding.Value[0];
                            Console.WriteLine($"\r\nPages including count: {imageDetail.PagesIncluding.Value.Count}");
                            Console.WriteLine($"First page content url: {firstPage.ContentUrl}");
                            Console.WriteLine($"First page name: {firstPage.Name}");
                            Console.WriteLine($"First page date published: {firstPage.DatePublished}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any pages including this image!");
                        }

                        // Related searches 
                        if (imageDetail.RelatedSearches?.Value?.Count > 0)
                        {
                            var firstRelatedSearch = imageDetail.RelatedSearches.Value[0];
                            Console.WriteLine($"\r\nRelated searches count: {imageDetail.RelatedSearches.Value.Count}");
                            Console.WriteLine($"First related search text: {firstRelatedSearch.Text}");
                            Console.WriteLine($"First related search web search url: {firstRelatedSearch.WebSearchUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any related searches!");
                        }

                        // Visually similar images
                        if (imageDetail.VisuallySimilarImages?.Value?.Count > 0)
                        {
                            var firstVisuallySimilarImage = imageDetail.VisuallySimilarImages.Value[0];
                            Console.WriteLine($"\r\nVisually similar images count: {imageDetail.RelatedSearches.Value.Count}");
                            Console.WriteLine($"First visually similar image name: {firstVisuallySimilarImage.Name}");
                            Console.WriteLine($"First visually similar image content url: {firstVisuallySimilarImage.ContentUrl}");
                            Console.WriteLine($"First visually similar image size: {firstVisuallySimilarImage.ContentSize}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any related searches!");
                        }

                        // Image tags
                        if (imageDetail.ImageTags?.Value?.Count > 0)
                        {
                            var firstTag = imageDetail.ImageTags.Value[0];
                            Console.WriteLine($"\r\nImage tags count: {imageDetail.ImageTags.Value.Count}");
                            Console.WriteLine($"First tag name: {firstTag.Name}");
                        }
                        else
                        {
                            Console.WriteLine("\r\nCouldn't find any related searches!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("\r\nCouldn't find detail about the image!");
                    }
                }
                else
                {
                    Console.WriteLine("\r\nCouldn't find image results!");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>Passos Seguintes

[Amostras do .NET SDK dos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)