---
title: Guia de introdução do Bing entidade pesquisa SDK Java | Microsoft Docs
description: Configure a aplicação de consola do SDK de pesquisa do Bing entidade.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/19/2018
ms.author: v-gedod
ms.openlocfilehash: ebfabc00b5dc031ac4e5284450a9d639c383e78f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355238"
---
# <a name="bing-entity-search-sdk-java-quickstart"></a>Guia de introdução do Bing entidade pesquisa SDK Java

O SDK de pesquisa do Bing entidade fornece a funcionalidade de REST API para consultas de entidade e os resultados da análise. 

O [código para exemplos do SDK de pesquisa do Java Bing entidade de origem](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch) está disponível no Hub de Git. 

## <a name="application-dependencies"></a>Dependências da aplicação
Obter um [chave de acesso de serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **pesquisa**. Instale as dependências do SDK de pesquisa do Bing entidade com o Maven, Gradle ou outro sistema de gestão de dependência. O ficheiro de Maven POM requer a declaração:
```
  <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-entitysearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies>
```
## <a name="entity-search-client"></a>Cliente de pesquisa de entidade
Adicione importações para a implementação de classe.
```
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
Implementar o **EntitySearchAPIImpl** cliente, que requer uma instância do **ServiceClientCredentials** classe.
```
public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
    return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
            new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                    builder.addNetworkInterceptor(
                            new Interceptor() {
                                @Override
                                public Response intercept(Interceptor.Chain chain) throws IOException {
                                    Request request = null;
                                    Request original = chain.request();
                                    // Request customization: add request headers
                                    Request.Builder requestBuilder = original.newBuilder()
                                            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                                    request = requestBuilder.build();
                                    return chain.proceed(request);
                                }
                            });
                }
            });
}

```
Procure a única entidade "Satya Nadella" e uma descrição breve de impressão.
```
public static void dominantEntityLookup(final String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);

        if (entityData.entities().value().size() > 0)
        {
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

            if(dominateEntry == null)
            {
                 System.out.println("Couldn't find main entity Satya Nadella!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Procure "William portas" e o identificador de resultados de disambiguation para a consulta é ambígua.
```
public static void handlingDisambiguation(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "william gates", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
        if (entityData.entities().value().size() > 0)
        {
            // Find the entity that represents the dominant entity
            List<Thing> entrys = entityData.entities().value();
            Thing dominateEntry = null;
            List<Thing> disambigEntities = new ArrayList<Thing>();
            for(Thing thing : entrys) {
                if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                    System.out.println("\r\nSearched for \"William Gates\" and found a dominant entity with this description:");
                    System.out.println(String.format("Searched for \"William Gates\" and found a dominant entity with type hint \"%s\" with this description:",
                            thing.entityPresentationInfo().entityTypeDisplayHint()));
                    System.out.println(thing.description());
                }

                if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DISAMBIGUATION_ITEM) {
                    disambigEntities.add(thing);
                    System.out.println("Searched for \"William Gates\" and found a dominant entity with this description:");
                    System.out.println(thing.description());
                }

            }

            if (dominateEntry == null)
            {
                 System.out.println("Couldn't find a reliable dominant entity for William Gates!");
            }

            if (disambigEntities.size() > 0)
            {
                 System.out.println();
                 System.out.println("This query is pretty ambiguous and can be referring to multiple things. Did you mean one of these: ");

                StringBuilder sb = new StringBuilder();

                for (Thing disambig : disambigEntities)
                {
                    sb.append(String.format(", or %s the %s", disambig.name(), disambig.entityPresentationInfo().entityTypeDisplayHint()));
                }

                 System.out.println(sb.toString().substring(5) + "?");
            }
            else
            {
                 System.out.println("We didn't find any disambiguation items for William Gates, so we must be certain what you're talking about!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Procurar um único arquivo com a consulta "Armazenar Microsoft" e o número de telefone para o resultado de impressão.
```
public static void storeLookup(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "Microsoft Store", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);

        if (entityData.places() != null && entityData.places().value().size() > 0)
        {
            // Some local entities are places, others are not. Depending on the data that you want, try to cast to the appropriate schema.
            // In this case, the returned item is technically a store, but the Place schema has the data that we want (telephone).
            Place store = (Place)entityData.places().value().get(0);

            if (store != null)
            {
                 System.out.println("\r\nSearched for \"Microsoft Store\" and found a store with this phone number:");
                 System.out.println(store.telephone());
            }
            else
            {
                 System.out.println("Couldn't find a place!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Procure uma lista de restaurants com a consulta "Seattle restaurants." Imprima os nomes e números de telefone para os resultados.
```
public static void multipleRestaurantLookup(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner restaurants = client.entities().search(
                "Seattle restaurants", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
        
        System.out.println("\r\nSearched for \"multiple restaurants\""); 
        if (restaurants.places() != null && restaurants.places().value().size() > 0)
        {
            List<Thing> listItems = new ArrayList<Thing>();
            for(Thing place : restaurants.places().value()) {
                if (place.entityPresentationInfo().entityScenario() == EntityScenario.LIST_ITEM) {
                    listItems.add(place);
                }
            }

            if (listItems.size() > 0)
            {
                StringBuilder sb = new StringBuilder();

                for (Thing item : listItems)
                {
                    Place place = (Place)item;
                    if (place == null)
                    {
                         System.out.println(String.format("Unexpectedly found something that isn't a place named \"%s\"", place.name()));
                        continue;
                    }

                    sb.append(String.format(",%s (%s) ", place.name(), place.telephone()));
                }

                 System.out.println("Ok, we found these places: ");
                 System.out.println(sb.toString().substring(1));
            }
            else
            {
                 System.out.println("Couldn't find any relevant results for \"seattle restaurants\"");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Adicione os métodos descritos neste artigo para uma classe com uma função principal para executar o código.
```
package entitySDK;
import com.microsoft.azure.cognitiveservices.entitysearch.*;

public class EntitySearchSDK {

    public static void main(String[] args) {
        
        dominantEntityLookup("YOUR-SUBSCRIPTION-KEY");
        handlingDisambiguation("YOUR-SUBSCRIPTION-KEY");
        restaurantLookup("YOUR-SUBSCRIPTION-KEY");
        multipleRestaurantLookup("YOUR-SUBSCRIPTION-KEY");

    }
    // Include the methods described in this article.
}
```
## <a name="next-steps"></a>Passos Seguintes

[Amostras do SDK de Java serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

