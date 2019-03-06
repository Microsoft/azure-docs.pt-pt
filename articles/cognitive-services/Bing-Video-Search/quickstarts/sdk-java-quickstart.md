---
title: 'Início rápido: Procurar vídeos com o SDK de pesquisa de vídeos do Bing para Java'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para Java.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 34555ec3315c1fd7322c249e59744cb512ac2d37
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437485"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Início rápido: Executar uma pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para Java

Utilize este guia de introdução para começar a pesquisa de notícias com o SDK de pesquisa de vídeo Bing para Java. Embora a pesquisa de vídeos do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), que contém mais anotações e funcionalidades de pesquisa de vídeos do Bing.

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit(JDK) de desenvolvimento do Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

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

2. Implementar o `VideoSearchAPIImpl` cliente, o que requer o ponto final de API e uma instância do `ServiceClientCredentials` classe.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar `ServiceClientCredentials`, siga estes passos:

    1. substituir a `applyCredentialsFilter()` função, com um `OkHttpClient.Builder` objeto como um parâmetro. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. Dentro `applyCredentialsFilter()`, chame `builder.addNetworkInterceptor()`. Criar uma nova `Interceptor` de objeto e substituir seu `intercept()` método tirar uma `Chain` objeto interceptor.

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

    3. Dentro do `intercept` de função, criar variáveis para o seu pedido. Utilize `Request.Builder()` para criar o seu pedido. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e retorno `chain.proceed()` no objeto de solicitação.
            
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

## <a name="send-a-search-request-and-receive-the-response"></a>Enviar um pedido de pesquisa e receber a resposta 

1. Criar uma função chamada `VideoSearch()` que usa a chave de subscrição como uma cadeia de caracteres. Criar uma instância do cliente de pesquisa que criou anteriormente.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Dentro `VideoSearch()`, enviar um pedido de pesquisa de vídeos com o cliente, com `SwiftKey` como o termo de pesquisa. Se a API de pesquisa de vídeo devolveu um resultado, obter o primeiro resultado e imprimir o respetivo id, name e URL, juntamente com o número total de vídeos devolvido. 
    
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

3. Chame o método de pesquisa do seu método principal.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a API de pesquisa de vídeos do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)