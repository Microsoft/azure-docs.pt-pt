---
title: Bing Entity Search Java client library quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 8c987aa14e922573d01aa35fab609edf01e109b4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136775"
---
Use este quickstart para começar a procurar entidades com a biblioteca de clientes Bing Entity Search para Java. Embora a Bing Entity Search tenha uma API REST compatível com a maioria dos idiomas de programação, a biblioteca do cliente fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Pré-requisitos

* O Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* A biblioteca de clientes Bing Entity Search para Java

Instale as dependências da biblioteca de clientes Bing Entity Search utilizando maven, Gradle ou outro sistema de gestão de dependência. O ficheiro POM do Maven requer a declaração:

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

1. Implementar `dominantEntityLookup` o cliente, que requer o seu ponto final `ServiceClientCredentials` da API, e uma instância da classe. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar `ServiceClientCredentials`o, siga estes passos:

   1. anular a `applyCredentialsFilter()` função, `OkHttpClient.Builder` com um objeto como parâmetro. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. Dentro, `applyCredentialsFilter()` `builder.addNetworkInterceptor()`chamada. Crie `Interceptor` um novo objeto `intercept()` e anule `Chain` o seu método para pegar num objeto intercetor.

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

   3. Dentro `intercept` da função, crie variáveis para o seu pedido. Use `Request.Builder()` para construir o seu pedido. Adicione a sua `Ocp-Apim-Subscription-Key` chave de `chain.proceed()` subscrição ao cabeçalho e devolva o objeto de pedido.
            
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

1. Crie uma nova instância do cliente de pesquisa com a sua chave de subscrição. usar `client.entities().search()` para enviar um pedido de `satya nadella`pesquisa para a consulta de pesquisa , e obter uma resposta. 
    
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

* [O que é a API de Pesquisa de Entidades Bing?](../../overview.md)
