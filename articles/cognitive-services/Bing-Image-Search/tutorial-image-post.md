---
title: 'Tutorial: Extrair detalhes de imagem com a API REST e C# - Pesquisa de imagem de Bing'
titleSuffix: Azure Cognitive Services
description: Utilize este tutorial para criar uma aplicação C# que extrai detalhes de imagem utilizando a API de Pesquisa de Imagem de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 5d455751dcabd67213566d605e83fda297e187d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316686"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Tutorial: extrair os detalhes da imagem através da API de Pesquisa de Imagens do Bing e C#

Existem múltiplos [pontos finais](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) disponíveis através da API de Pesquisa de Imagens do Bing. O ponto final `/details` aceita um pedido POST com uma imagem e pode devolver uma série de detalhes sobre a imagem. Esta aplicação C# envia uma imagem através desta API e apresenta os detalhes devolvidos pelo Bing, que são objetos JSON como os seguintes:

![[resultados JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Este tutorial explica como:

> [!div class="checklist"]
> * Utilizar o ponto final `/details` da Pesquisa de Imagens num pedido `POST`
> * Especificar cabeçalhos para o pedido
> * Utilizar os parâmetros de URL para especificar resultados
> * Carregar os dados da imagem e enviar o pedido `POST`
> * Imprimir os resultados JSON para a consola

O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Estúdio Visual 2017 ou posterior.](https://visualstudio.microsoft.com/downloads/)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Criar um pedido de pesquisa de detalhes da imagem

Segue-se o ponto final `/details`, que aceita pedidos POST com dados da imagem no corpo do pedido. Pode utilizar o ponto final global abaixo ou o ponto final [personalizado subdomínio](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Ao criar o URL do pedido de pesquisa, o parâmetro `modules` segue o ponto final acima e especifica os tipos de detalhes que os resultados terão:

* `modules=All`
* `modules=RecognizedEntities` (pessoas ou locais visíveis na imagem)

Especifique `modules=All` no pedido POST para obter o texto JSON que inclui o seguinte:

* `bestRepresentativeQuery` – uma consulta do Bing que devolve imagens semelhantes à imagem carregada
* `detectedObjects` – objetos encontrados na imagem
* `image` – metadados da imagem
* `imageInsightsToken` – um token para pedidos GET posteriores que obtêm `RecognizedEntities` (pessoas ou locais visíveis na imagem) da imagem.
* `imageTags` – etiquetas da imagem
* `pagesIncluding` – páginas Web que incluam a imagem
* `relatedSearches` – pesquisas com base nos detalhes na imagem.
* `visuallySimilarImages` – imagens semelhantes na Web.

Especifique `modules=RecognizedEntities` no pedido POST para obter apenas `imageInsightsToken`, que pode ser utilizado num pedido GET subsequente para identificar pessoas ou locais na imagem.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Criar um objeto WebClient e configurar cabeçalhos para o pedido da API

Crie um objeto `WebClient` e configure os cabeçalhos. Todos os pedidos para a API de Pesquisa do Bing necessitam de uma `Ocp-Apim-Subscription-Key`. Um pedido `POST` para carregar uma imagem também tem de especificar o `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Carregar a imagem e apresentar os resultados

O método `UpLoadFile()` da classe `WebClient` que formata os dados para o pedido `POST`, incluindo formatar o `RequestStream` e chamar o `HttpWebRequest`.

Chame o `WebClient.UpLoadFile()` com o ponto final `/details` e o ficheiro de imagem a carregar. Utilize a resposta JSON para inicializar uma instância da estrutura `SearchResult` e armazene a resposta.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Em seguida, esta resposta JSON pode ser impressa na consola.

## <a name="use-an-image-insights-token-in-a-request"></a>Utilizar o token de informações da imagem num pedido

Para utilizar o `ImageInsightsToken` devolvido com os resultados de um pedido `POST`, pode adicioná-lo a um pedido `GET`. Por exemplo:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Se existirem pessoas ou locais identificáveis na imagem, este pedido devolverá informações sobre os mesmos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Apresentar opções de pesquisa e imagens numa aplicação Web de página única ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte também

* [Bing Image Search API reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
