---
title: Bing Video Search Java biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2b3d4993406f150b2983d4d820f7d070b5de1e96
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375796"
---
Use este quickstart para começar a procurar notícias com a biblioteca de clientes Bing Video Search para Java. Enquanto a Bing Video Search tem uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente fornece uma maneira fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub,](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch)com anotações adicionais e características.

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit de Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Biblioteca de Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Instale as dependências da biblioteca do cliente Bing Video Search utilizando Maven, Gradle ou outro sistema de gestão de dependência. O ficheiro POM do Maven necessita da seguinte declaração:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto


Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Implemente o `VideoSearchAPIImpl` cliente, que requer o seu ponto final da API, e uma instância da `ServiceClientCredentials` classe.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para `ServiceClientCredentials` implementar, siga estes passos:

    1. sobrepor a `applyCredentialsFilter()` função, com um `OkHttpClient.Builder` objeto como parâmetro. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. `applyCredentialsFilter()`Dentro, `builder.addNetworkInterceptor()` ligue. Crie um novo `Interceptor` objeto e substitua o seu `intercept()` método para pegar um objeto `Chain` intercetor.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. Dentro da `intercept` função, crie variáveis para o seu pedido. Use `Request.Builder()` para construir o seu pedido. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho e `chain.proceed()` devolva no objeto pedido.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>Envie um pedido de pesquisa e receba a resposta 

1. Crie uma função chamada `VideoSearch()` que leva a sua chave de subscrição como uma corda. Instantiizar o cliente de pesquisa criado anteriormente.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Dentro `VideoSearch()` , Envie um pedido de pesquisa de vídeo usando o cliente, com o termo de `SwiftKey` pesquisa. Se a API de Pesquisa de Vídeo devolveu um resultado, obtenha o primeiro resultado e imprima o seu id, nome e URL, juntamente com o número total de vídeos devolvidos. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Ligue para o método de pesquisa a partir do seu método principal.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de uma página única](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Veja também 

* [O que é a API de Pesquisa de Vídeos do Bing?](../../overview.md)
* [Serviços cognitivos .NET SDK amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
