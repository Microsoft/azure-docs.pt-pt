---
title: 'Início rápido: Verifique a ortografia com a API REST de verificação ortográfica do Bing e o Java'
titlesuffix: Azure Cognitive Services
description: Começar a utilizar a API de REST de verificação do Bing ortográfica para verificar a ortografia e gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888989"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Início rápido: Verifique a ortografia com a API REST de verificação ortográfica do Bing e o Java

Utilize este guia de introdução para efetuar a primeira chamada à API do REST Bing de verificação de ortográfica. Esta aplicação de Java simple envia um pedido para a API e devolve uma lista de correções sugeridas. Embora esse aplicativo é escrito em Java, a API é um serviço RESTful web compatível com a maioria das linguagens de programação. O código-fonte para esta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Pré-requisitos

Kit(JDK) de desenvolvimento Java 7 ou posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>Criar e inicializar uma aplicação

1. Criar um novo projeto de Java no seu IDE ou editor favorito e importe os seguintes pacotes.

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Crie variáveis para anfitrião do ponto final de API, caminho e a chave de subscrição. Em seguida, crie variáveis para o mercado, o texto que pretende para a verificação ortográfica e uma cadeia de caracteres para o modo de verificação ortográfica.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Criar e enviar um pedido de API

1. Criar uma função chamada `check()` para criar e enviar o pedido de API. Dentro da mesma, siga estes passos. Crie uma cadeia de caracteres para os parâmetros do pedido. Acrescentar a `?mkt=` parâmetro para a sua cadeia de caracteres do mercado e o `&mode=` parâmetro para o seu modo de verificação ortográfica.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. Crie um URL ao combinar a cadeia de anfitrião, caminho e parâmetros do ponto final. Criar um novo `HttpsURLConnection` obejct.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. Abra uma ligação para o URL. Definir o método de pedido como `POST`. Adicione os parâmetros do pedido. Certifique-se de adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho. 

    ```java
    url.openConnection();
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Criar um novo `DataOutputStream` de objeto e enviar o pedido para a API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="read-the-response"></a>Ler a resposta

1. Criar um `BufferedReader` e ler a resposta da API. Imprimi-lo na consola.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. A função main de seu aplicativo, chame a função criada acima. 

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```
    
## <a name="example-json-response"></a>Resposta JSON de exemplo

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorials/spellcheck.md)

- [O que é a API de verificação de ortografia do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
