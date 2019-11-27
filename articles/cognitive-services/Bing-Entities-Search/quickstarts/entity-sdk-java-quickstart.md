---
title: 'Início rápido: Pesquisar entidades com o SDK para Java-Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar entidades com o SDK do Pesquisa de Entidade do Bing para Java
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: f39355613382bd5d02b7ef6918598f38737e850f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327141"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Início rápido: Enviar uma solicitação de pesquisa com o SDK do Pesquisa de Entidade do Bing para Java

Use este guia de início rápido para começar a procurar entidades com o SDK do Pesquisa de Entidade do Bing para Java. Embora Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Pré-requisitos

* O [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* O SDK do Pesquisa de Entidade do Bing para Java

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

2. Criar uma variável para sua chave de assinatura

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Implemente o `dominantEntityLookup` Client, que exige o ponto de extremidade da API e uma instância da classe `ServiceClientCredentials`.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar o `ServiceClientCredentials`, siga estas etapas:

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
      ## <a name="send-a-request-and-receive-a-response"></a>Enviar uma solicitação e receber uma resposta

1. Crie uma nova instância do cliente de pesquisa com sua chave de assinatura. Use `client.entities().search()` para enviar uma solicitação de pesquisa para o `satya nadella`de consulta de pesquisa e obter uma resposta. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Se todas as entidades forem retornadas, converta-as em uma lista. Itere-as e imprima a entidade dominante.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é o API de Pesquisa de Entidade do Bing?](../overview.md )
