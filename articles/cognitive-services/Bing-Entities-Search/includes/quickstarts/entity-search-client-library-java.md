---
title: Bing Entidade Pesquisar Java biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: f69b9b989a93949f9a0441676c81af7480fb968f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375818"
---
Use este quickstart para começar a procurar entidades com a biblioteca de clientes Bing Entity Search para Java. Embora a Bing Entity Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit de Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* A biblioteca de clientes de pesquisa de entidades Bing para Java

Instale as dependências da biblioteca de clientes Bing Entity Search utilizando Maven, Gradle ou outro sistema de gestão de dependência. O ficheiro POM do Maven requer a declaração:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


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

2. Crie uma variável para a sua chave de subscrição

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Implemente o `dominantEntityLookup` cliente, que requer o seu ponto final da API, e uma instância da `ServiceClientCredentials` classe. Pode utilizar o ponto final global abaixo ou o ponto final [personalizado subdomínio](../../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar os `ServiceClientCredentials` passos, siga estes passos:

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
## <a name="send-a-request-and-receive-a-response"></a>Enviar um pedido e receber uma resposta

1. Crie uma nova instância do cliente de pesquisa com a sua chave de subscrição. usar `client.entities().search()` para enviar um pedido de pesquisa para a consulta de pesquisa , e obter uma `satya nadella` resposta. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Se alguma entidade for devolvida, converta-as numa lista. Iterar através deles, e imprimir a entidade dominante.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade Bing?](../../overview.md)
