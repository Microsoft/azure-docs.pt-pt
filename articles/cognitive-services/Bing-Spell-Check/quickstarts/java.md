---
title: 'Quickstart: Verifique a ortografia com a API REST e Java - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Começa a usar a API E a Java do Bing Spell Check para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e74954e33be1b2d24219ca61762dd43941415306
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366983"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Quickstart: Verifique a ortografia com a API e Java do Bing Spell Check REST e Java

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este arranque rápido para fazer a sua primeira chamada para a API Bing Spell Check REST. Esta simples aplicação Java envia um pedido à API e devolve uma lista de correções sugeridas. 

Embora esta aplicação esteja escrita em Java, a API é um serviço web RESTful compatível com a maioria das linguagens de programação. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Pré-requisitos

* O Kit de Desenvolvimento de Java (JDK) 7 ou mais tarde.

* Importe o [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) ou a versão [Gson](https://github.com/google/gson) mais atual. Para a execução da linha de comando, adicione `.jar` a pasta Java à classe principal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Criar e inicializar uma aplicação

1. Crie um novo Projeto Java no seu IDE favorito ou editor com um nome de classe à sua escolha e, em seguida, importe os seguintes pacotes:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Crie variáveis para o anfitrião, caminho e a chave de subscrição do ponto final da API. Em seguida, crie variáveis para o seu mercado, o texto que pretende soletrar verificação, e uma cadeia para o modo de verificação ortográfica. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Criar e enviar um pedido de API

1. Criar uma função chamada `check()` para criar e enviar o pedido de API. Dentro desta função, adicione o código especificado nos próximos passos. Criar uma cadeia para os parâmetros de pedido:

   1. Atribua o seu código de mercado ao `mkt` parâmetro com o `=` operador. 

   1. Adicione o `mode` parâmetro com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Crie um URL combinando o hospedeiro, o caminho e a cadeia de parâmetros. Criar um `HttpsURLConnection` novo objeto.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Abra uma ligação ao URL. Desa estada o método de pedido `POST` e adicione os parâmetros de pedido. Certifique-se de adicionar a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Crie um novo `DataOutputStream` objeto e envie o pedido para a API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formato e ler a resposta da API

1. Adicione o `prettify()` método à sua classe, que formata o JSON para uma saída mais legível.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Crie uma `BufferedReader` e leia a resposta da API. Imprima na consola.
    
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

Na função principal da sua aplicação, ligue para o seu `check()` método criado anteriormente.
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

Construa e execute o seu projeto. Se estiver a utilizar a linha de comando, utilize os seguintes comandos para construir e executar a aplicação:

1. Construa a aplicação:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Execute a aplicação:

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>Exemplo JSON resposta

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check Referência V7 da API](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)