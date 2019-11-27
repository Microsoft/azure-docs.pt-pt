---
title: 'Início rápido: executar uma pesquisa de notícias usando o SDK para Java-Pesquisa de Notícias do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar notícias usando o SDK do Pesquisa de Notícias do Bing para Java e processe a resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: def964dd932f8a41a877a3d9568a8ec4825874b8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383920"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Início rápido: Pesquisar notícias com o SDK do Pesquisa de Notícias do Bing para Java

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Notícias do Bing para Java. Embora Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Pré-requisitos

Instale as dependências do SDK do Pesquisa de Notícias do Bing usando o Maven, o gradle ou outro sistema de gerenciamento de dependência. O ficheiro POM do Maven necessita da seguinte declaração:

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

## <a name="create-a-search-client-and-store-credentials"></a>Criar um cliente de pesquisa e armazenar credenciais

1. Crie um método chamado `getClient()` que retorna um novo cliente de pesquisa de `NewsSearchAPIImpl`. Adicione seu ponto de extremidade como o primeiro parâmetro para o novo objeto`NewsSearchAPIImpl` e um novo objeto `ServiceClientCredentials` para armazenar suas credenciais.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Para criar o objeto `ServiceClientCredentials`, substitua a função `applyCredentialsFilter()`. Passe um `OkHttpClient.Builder` para o método e use o método `addNetworkInterceptor()` do construtor para criar suas credenciais para a chamada do SDK.

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

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa

1. Crie um método que chama `getClient()` e envia uma solicitação de pesquisa para o serviço Pesquisa de Notícias do Bing. Filtre a pesquisa com os parâmetros de *mercado* e de *contagem* e, em seguida, imprima informações sobre o primeiro resultado de notícias: nome, URL, data de publicação, descrição, nome do provedor e número total de correspondências estimadas para sua pesquisa.

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

2. Adicione o método de pesquisa a um método de `main()` para executar o código.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
