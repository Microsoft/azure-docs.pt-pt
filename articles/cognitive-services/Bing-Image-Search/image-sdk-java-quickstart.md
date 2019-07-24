---
title: 'Início rápido: Pesquisar por imagens-SDK de Pesquisa de Imagem do Bing para Java'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK da Pesquisa de Imagens do Bing, um wrapper para a API que contém as mesmas funcionalidades. Esta aplicação Java simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9b756d097f313179f4cc177a8cbe74419629317b
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423973"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Início rápido: Pesquisar imagens com o SDK do Pesquisa de Imagem do Bing para Java

Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK da Pesquisa de Imagens do Bing, um wrapper para a API que contém as mesmas funcionalidades. Esta aplicação Java simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos
Obtenha uma [chave de acesso aos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisa**.  Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

A versão mais recente do [Java Development Kit](https://aka.ms/azure-jdks) (JDK)

Instale as dependências do SDK da Pesquisa de Imagens do Bing com o Maven, Gradle ou outro sistema de gestão de dependências. O ficheiro POM do Maven necessita da seguinte declaração:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo projeto Java em seu IDE ou editor favorito e adicione as seguintes importações à implementação de sua classe:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. No método principal, crie variáveis para a sua chave de subscrição e termo de pesquisa. Em seguida, instancie o cliente Pesquisa de Imagens do Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Enviar uma solicitação de pesquisa para a API

1. Utilize `bingImages().search()` para enviar o pedido HTTP que contém a consulta de pesquisa. Guarde a resposta como `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analisar e ver o resultado

Analise os resultados da imagem devolvidos na resposta.
Se a resposta contiver resultados da pesquisa, armazene o primeiro resultado e imprima seus detalhes, como uma URL de miniatura, a URL original, junto com o número total de imagens retornadas.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Amostras de Java para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
