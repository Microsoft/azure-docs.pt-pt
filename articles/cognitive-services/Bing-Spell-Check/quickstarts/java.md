---
title: 'Início rápido: verificar a ortografia com a API REST e o Java-Verificação Ortográfica do Bing'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST do Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 0a40163eddffd4e406dd0644f950f6af636a1dfa
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383878"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Início rápido: verificar a ortografia com a API REST do Verificação Ortográfica do Bing e o Java

Use este guia de início rápido para fazer sua primeira chamada para a API REST do Verificação Ortográfica do Bing. Esse aplicativo Java simples envia uma solicitação para a API e retorna uma lista de correções sugeridas. Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Pré-requisitos

* O Java Development Kit (JDK) 7 ou posterior.

* Importe o [Gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) ou a versão mais atual do [Gson](https://github.com/google/gson) . Para a execução da linha de comando, adicione o `.jar` à sua pasta Java com a classe principal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Criar e inicializar um aplicativo

1. Crie um novo projeto Java em seu IDE ou editor favorito com um nome de classe de sua escolha e, em seguida, importe os pacotes a seguir.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Crie variáveis para o host do ponto de extremidade da API, o caminho e sua chave de assinatura. Em seguida, crie variáveis para o seu mercado, o texto no qual você deseja verificar a ortografia e uma cadeia de caracteres para o modo de verificação ortográfica.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Criar e enviar uma solicitação de API

1. Crie uma função chamada `check()` para criar e enviar a solicitação de API. Dentro dele, siga estas etapas. Crie uma cadeia de caracteres para os parâmetros de solicitação. Acrescente o parâmetro `?mkt=` à sua cadeia de caracteres de mercado e o parâmetro `&mode=` ao modo de verificação ortográfica.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Crie uma URL combinando o host do ponto de extremidade, o caminho e a cadeia de parâmetros. Crie um novo objeto `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Abra uma conexão com a URL. Defina o método de solicitação como `POST`. Adicione seus parâmetros de solicitação. Certifique-se de adicionar sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Crie um novo objeto `DataOutputStream` e envie a solicitação para a API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatar e ler a resposta da API

1. Adicione este método à sua classe. Ele formata o JSON para uma saída mais legível.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Crie um `BufferedReader` e leia a resposta da API. Imprima-o no console do.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Chamar a API

Na função principal do seu aplicativo, chame o método check () criado acima.
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

## <a name="run-the-application"></a>Executar a aplicação

Compile e execute seu projeto.

Se você estiver usando a linha de comando, use os comandos a seguir para compilar e executar o aplicativo.

**Integrado**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Funcionam**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Exemplo de resposta JSON

O JSON devolve uma resposta de êxito, conforme apresentado no exemplo seguinte:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
