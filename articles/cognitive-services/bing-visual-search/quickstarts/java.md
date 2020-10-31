---
title: 'Quickstart: Obtenha insights de imagem usando a API REST e Java - Pesquisa Visual Bing'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual Bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: scottwhi
ms.openlocfilehash: f33b5f5274fcaf8f64f898e2b7aebe8a27470033
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086538"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Quickstart: Obtenha insights de imagem usando a API e Java de Pesquisa Visual Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing. Esta aplicação Java envia uma imagem para a API e exibe a informação que retorna. Embora esta aplicação esteja escrita em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit de Desenvolvimento de Java (JDK) 7 ou 8](https://aka.ms/azure-jdks)
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

2. Crie variáveis para o seu ponto final da API, chave de subscrição e o caminho para a sua imagem. Pelo `endpoint` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
3. Ao carregar uma imagem local, os dados do formulário devem incluir o `Content-Disposition` cabeçalho. Desaveie o seu `name` parâmetro para "imagem", e desave o `filename` parâmetro para o nome do ficheiro da imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>Criar o analisador JSON

Crie um método para tornar a resposta JSON da API mais legível através da utilização `JsonParser` .

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>Criar a consulta e o pedido de pesquisa

1. No método principal da sua aplicação, crie um cliente HTTP utilizando `HttpClientBuilder.create().build();` .

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Crie um `HttpEntity` objeto para fazer o upload da sua imagem para a API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Crie um `httpPost` objeto com o seu ponto final e desemote o cabeçalho para utilizar a sua chave de subscrição.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Receber e processar a resposta JSON

1. Utilize o `HttpClient.execute()` método para enviar um pedido à API e armazenar a resposta num `InputStream` objeto.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Guarde a corda JSON e imprima a resposta.

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
