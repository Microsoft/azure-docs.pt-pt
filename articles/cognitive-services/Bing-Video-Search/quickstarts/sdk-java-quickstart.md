---
title: 'Início rápido: pesquisar vídeos usando o SDK para Java-Pesquisa de Vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar solicitações de pesquisa de vídeo usando o SDK do Pesquisa de Vídeo do Bing para Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 8222a940909b98fbcce9797fe2d59f0bfb554526
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448346"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Início rápido: executar uma pesquisa de vídeo com o SDK do Pesquisa de Vídeo do Bing para Java

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Vídeo do Bing para Java. Embora Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), com anotações adicionais e recursos.

## <a name="prerequisites"></a>Pré-requisitos

* O [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Biblioteca de Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Instale as dependências de SDK de Pesquisa de Vídeos do Bing com o Maven, Gradle ou outro sistema de gestão de dependências. O ficheiro POM do Maven necessita da seguinte declaração:

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

1. Implemente o `VideoSearchAPIImpl` Client, que exige o ponto de extremidade da API e uma instância da classe `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar `ServiceClientCredentials`, siga estas etapas:

    1. Substitua a função `applyCredentialsFilter()`, por um objeto `OkHttpClient.Builder` como um parâmetro. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. Em `applyCredentialsFilter()`, chame `builder.addNetworkInterceptor()`. Crie um novo objeto `Interceptor` e substitua seu método `intercept()` para pegar um objeto interceptador `Chain`.

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

    3. Dentro da função `intercept`, crie variáveis para sua solicitação. Use `Request.Builder()` para compilar sua solicitação. Adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key` e retorne `chain.proceed()` no objeto de solicitação.
            
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

## <a name="send-a-search-request-and-receive-the-response"></a>Enviar uma solicitação de pesquisa e receber a resposta 

1. Crie uma função chamada `VideoSearch()` que usa sua chave de assinatura como uma cadeia de caracteres. Crie uma instância do cliente de pesquisa criado anteriormente.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Em `VideoSearch()`, envie uma solicitação de pesquisa de vídeo usando o cliente, com `SwiftKey` como o termo de pesquisa. Se a API de Pesquisa de Vídeo retornou um resultado, obtenha o primeiro resultado e imprima sua ID, nome e URL, juntamente com o número total de vídeos retornados. 
    
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

3. Chame o método Search do método Main.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

* [O que é o API de Pesquisa de Vídeo do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)