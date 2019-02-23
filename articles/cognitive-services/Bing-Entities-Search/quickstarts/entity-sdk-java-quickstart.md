---
title: 'Início rápido: Pesquisa de entidades com o SDK de pesquisa de entidades do Bing para Java'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para procurar entidades com o SDK de pesquisa de entidades do Bing para Java
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: ea13ae5bc649ae3a803fb8446fa009fac94874a8
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673569"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Início rápido: Enviar um pedido de pesquisa com o SDK de pesquisa de entidades do Bing para Java

Utilize este guia de introdução para começar a pesquisa de entidades com o SDK de pesquisa de entidades do Bing para Java. Embora a pesquisa de entidades do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit(JDK) de desenvolvimento do Java](https://www.oracle.com/technetwork/java/javase/downloads/)

* A pesquisa de entidades do Bing SDK para Java

Instale as dependências do SDK de Pesquisa de Entidades do Bing com o Maven, Gradle ou outro sistema de gestão de dependências. O ficheiro POM do Maven requer a declaração:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Criar uma variável para a sua chave de subscrição

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Criar um cliente de pesquisa

2. Implementar o `dominantEntityLookup` cliente, o que requer o ponto final de API e uma instância do `ServiceClientCredentials` classe.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar o `ServiceClientCredentials`, siga estes passos:

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
## <a name="send-a-request-and-receive-a-response"></a>Enviar um pedido e receber uma resposta

1. Crie uma nova instância do cliente de pesquisa com a sua chave de subscrição. utilizar `client.entities().search()` para enviar uma solicitação de pesquisa para a consulta de pesquisa `satya nadella`e obter uma resposta. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

2. Se qualquer entidades foram devolvidas, convertê-los numa lista. Iterá-los e a entidade dominante de impressão.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )
