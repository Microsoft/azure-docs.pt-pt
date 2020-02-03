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
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 49e3492bae1dfe248e07227caff11a6d00e59c08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716468"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Início rápido: Enviar uma solicitação de pesquisa com o SDK do Pesquisa de Entidade do Bing para Java

Use este guia de início rápido para começar a procurar entidades com o SDK do Pesquisa de Entidade do Bing para Java. Embora Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Pré-requisitos

* O Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

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

1. Implementar o cliente `dominantEntityLookup`, que requer o seu ponto final da API, e uma instância da classe `ServiceClientCredentials`. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Para implementar o `ServiceClientCredentials`, siga estes passos:

   1. anular a função `applyCredentialsFilter()`, com um objeto `OkHttpClient.Builder` como parâmetro. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. Dentro `applyCredentialsFilter()`, ligue `builder.addNetworkInterceptor()`. Crie um novo objeto `Interceptor` e sobreponha-se ao seu método `intercept()` para tomar um objeto intercetor `Chain`.

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

   3. Dentro da função `intercept`, crie variáveis para o seu pedido. Use `Request.Builder()` para construir o seu pedido. Adicione a sua chave de subscrição ao cabeçalho `Ocp-Apim-Subscription-Key` e devolva `chain.proceed()` no objeto de pedido.
            
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

1. Crie uma nova instância do cliente de pesquisa com sua chave de assinatura. use `client.entities().search()` para enviar um pedido de pesquisa para a consulta de pesquisa `satya nadella`, e obter uma resposta. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Se todas as entidades forem retornadas, converta-as em uma lista. Itere-as e imprima a entidade dominante.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de uma página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidades Bing?](../overview.md )
