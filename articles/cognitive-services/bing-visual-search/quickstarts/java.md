---
title: 'Início rápido: obter informações de imagem usando a API REST e Java-Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem no API da Pesquisa Visual do Bing e obter informações sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: eecca2372c7265d456276a966cc441b15c17272a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383608"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Início rápido: obter informações de imagem usando a API REST do Pesquisa Visual do Bing e o Java

Use este guia de início rápido para fazer sua primeira chamada para a API da Pesquisa Visual do Bing e exibir os resultados. Esse aplicativo Java carrega uma imagem na API e exibe as informações que ele retorna. Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Quando você carrega uma imagem local, os dados do formulário devem incluir o cabeçalho `Content-Disposition`. Você deve definir seu parâmetro `name` como "Image" e pode definir o parâmetro `filename` como qualquer cadeia de caracteres. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo da imagem que você pode carregar é 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* O [Java Development Kit (JDK) 7 ou 8](https://aka.ms/azure-jdks)
* A [biblioteca Gson Java](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java em seu IDE ou editor favorito e importe as seguintes bibliotecas:

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

2. Crie variáveis para seu ponto de extremidade de API, chave de assinatura e o caminho para a imagem:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Criar o analisador JSON

Crie um método para tornar a resposta JSON da API mais legível usando `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Criar a consulta e o pedido de pesquisa

1. No método principal de seu aplicativo, crie um cliente HTTP usando `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Crie um objeto `HttpEntity` para carregar a imagem na API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Crie um objeto `httpPost` com seu ponto de extremidade e defina o cabeçalho para usar sua chave de assinatura:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Receber e processar a resposta JSON

1. Use o método `HttpClient.execute()` para enviar uma solicitação para a API e armazenar a resposta em um objeto `InputStream`:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Armazene a cadeia de caracteres JSON e imprima a resposta:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um Pesquisa Visual aplicativo Web de página única](../tutorial-bing-visual-search-single-page-app.md)
