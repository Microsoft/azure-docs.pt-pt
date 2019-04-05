---
title: 'Início rápido: Obtenha informações de imagem usando a API de REST de pesquisa Visual do Bing e o Java'
titleSuffix: Azure Cognitive Services
description: Aprenda a carregar uma imagem para a API de pesquisa Visual do Bing e obter conhecimentos sobre ele.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 2fe4e9dad0b198fe54e06ce07100d231f1f7d157
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046449"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Início rápido: Obtenha informações de imagem usando a API de REST de pesquisa Visual do Bing e o Java

Utilize este guia de introdução para tornar a sua primeira chamada à API de pesquisa Visual do Bing e ver os resultados. Esta aplicação de Java carrega uma imagem para a API e apresenta as informações que ele retorna. Embora esse aplicativo é escrito em Java, a API é um serviço RESTful Web compatível com a maioria das linguagens de programação.

Ao carregar uma imagem do local, os dados do formulário tem de incluir o `Content-Disposition` cabeçalho. Tem de definir seu `name` parâmetro para "imagem" e pode definir o `filename` parâmetro para qualquer cadeia de caracteres. O conteúdo do formulário inclui os dados binários da imagem. O tamanho da imagem máximo que pode carregar é de 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit de desenvolvimento Java (JDK) 7 ou 8](https://aka.ms/azure-jdks)
* O [biblioteca Gson Java](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Criar um novo projeto de Java no seu IDE ou editor favorito e importar as seguintes bibliotecas:

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

2. Crie variáveis para o ponto final de API, a chave de subscrição e o caminho para a sua imagem:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Criar o analisador JSON

Criar um método para tornar a resposta JSON da API mais legível utilizando `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Criar a consulta e o pedido de pesquisa

1. O método principal de seu aplicativo, criar um cliente HTTP utilizando `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Criar um `HttpEntity` objeto para carregar a imagem para a API:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Criar um `httpPost` objeto com o ponto final e definir o cabeçalho para utilizar a sua chave de subscrição:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Receber e processar a resposta JSON

1. Utilize o `HttpClient.execute()` método para enviar um pedido para a API e armazenar a resposta num `InputStream` objeto:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Store a cadeia de caracteres do JSON e a resposta de impressão:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação de web de página única de pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
