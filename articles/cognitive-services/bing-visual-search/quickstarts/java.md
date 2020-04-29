---
title: 'Quickstart: Obtenha insights de imagem usando a REST API e Java - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446620"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Quickstart: Obtenha insights de imagem usando a API e Java de Pesquisa Visual Bing

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing e veja os resultados. Esta aplicação Java envia uma imagem para a API e exibe a informação que devolve. Embora esta aplicação esteja escrita em Java, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O Kit de [Desenvolvimento java (JDK) 7 ou 8](https://aka.ms/azure-jdks)
* A [biblioteca Gson Java](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java no seu IDE ou editor favorito, e importe as seguintes bibliotecas:

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Crie variáveis para o seu ponto final de API, chave de subscrição e o caminho para a sua imagem. `endpoint`pode ser o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Ao fazer o upload de uma imagem `Content-Disposition` local, os dados do formulário devem incluir o cabeçalho. Deve definir `name` o parâmetro para "imagem", e `filename` pode definir o parâmetro em qualquer corda. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>Crie o parser JSON

Criar um método para tornar a resposta JSON `JsonParser`da API mais legível utilizando:

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>Criar a consulta e o pedido de pesquisa

1. No método principal da sua aplicação, `HttpClientBuilder.create().build();`crie um cliente HTTP utilizando:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Crie `HttpEntity` um objeto para fazer o upload da sua imagem para a API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Crie `httpPost` um objeto com o seu ponto final e detetete teo cabeçalho para utilizar a sua chave de subscrição:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Receber e processar a resposta JSON

1. Utilize `HttpClient.execute()` o método para enviar um pedido à API `InputStream` e guarde a resposta num objeto:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Guarde a corda JSON e imprima a resposta:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
