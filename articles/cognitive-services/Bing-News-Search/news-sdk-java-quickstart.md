---
title: 'Início rápido: Efetua uma pesquisa de notícias - SDK de pesquisa de notícias do Bing para Java'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para pesquisar notícias com o SDK de pesquisa de notícias do Bing para Java e processar a resposta.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 648e1701154c725a0645bd520045238030a71ea9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108700"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Início rápido: Pesquisa de notícias com o SDK de pesquisa de notícias do Bing para Java

Utilize este guia de introdução para começar a pesquisa de notícias com o SDK de pesquisa de notícias do Bing para Java. Embora a pesquisa do Bing notícias tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Pré-requisitos

Instale as dependências de SDK de pesquisa de notícias do Bing com o Maven, Gradle ou outro sistema de gestão de dependência. O ficheiro POM do Maven necessita da seguinte declaração:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Criar uma pesquisa de credenciais de cliente e de arquivo

1. Criar um método chamado `getClient()` que retorna um novo `NewsSearchAPIImpl` cliente de pesquisa. Adicionar o ponto final que o primeiro parâmetro para o novo`NewsSearchAPIImpl` objeto e um novo `ServiceClientCredentials` objeto para armazenar as suas credenciais.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Para criar o `ServiceClientCredentials` objeto, substituir o `applyCredentialsFilter()` função. Passar um `OkHttpClient.Builder` para o método e do uso o construtor `addNetworkInterceptor()` método para criar as suas credenciais para a chamada SDK.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa

1. Criar um método que chama `getClient()` e envia um pedido de pesquisa para o serviço de pesquisa de notícias do Bing. Filtrar a pesquisa com o *mercado* e *contagem* parâmetros, imprimir, em seguida, obter informações sobre o primeiro resultado de notícias: nome, URL, data de publicação, descrição, nome do fornecedor e o número total de estimado correspondências para a sua pesquisa.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Adicione o método de pesquisa para um `main()` método para executar o código.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
