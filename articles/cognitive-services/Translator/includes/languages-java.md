---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 34ff0e792fc388f3083e2d490b2658822793988f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906924"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com Gradle

Vamos começar por criar um diretório de trabalho para este projeto. A partir da linha de comando (ou terminal), executar este comando:

```console
mkdir get-languages-sample
cd get-languages-sample
```

A seguir, vais rubricar um projeto Gradle. Este comando criará ficheiros de construção essenciais para gradle, o mais importante, o `build.gradle.kts`, que é usado no tempo de execução para criar e configurar a sua aplicação. Execute este comando a partir do seu diretório de trabalho:

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

## <a name="configure-the-build-file"></a>Configure o ficheiro de construção

Localize-o `build.gradle.kts` e abra-o com o seu IDE favorito ou editor de texto. Em seguida, copiar nesta configuração de construção:

```java
plugins {
    java
    application
}
application {
    mainClassName = "GetLanguages"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Tome nota que esta amostra tem dependências de OkHttp para pedidos HTTP, e Gson para lidar e analisar JSON. Se quiser saber mais sobre configurações de construção, consulte [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Criar um ficheiro Java

Vamos criar uma pasta para a sua aplicação de amostras. Do seu diretório de trabalho, corra:

```console
mkdir -p src/main/java
```

Em seguida, nesta pasta, `GetLanguages.java`crie um ficheiro chamado .

## <a name="import-required-libraries"></a>Importação de bibliotecas necessárias

Abra `GetLanguages.java` e adicione estas declarações de importação:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Definir variáveis

Primeiro, terá de criar uma classe pública para o seu projeto:

```java
public class GetLanguages {
  // All project code goes here...
}
```

Adicione estas linhas `GetLanguages` à classe. Você vai notar que a chave de subscrição e o ponto final estão sendo lidos a partir de variáveis ambientais:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/languages?api-version=3.0";
```

Se estiver a utilizar uma subscrição multi-serviço `Ocp-Apim-Subscription-Region` de Serviços Cognitivos, também deve incluir os parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e construir um pedido

Adicione esta linha `GetLanguages` à classe `OkHttpClient`para instantaneamente:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos `GET` construir o pedido.

```java
// This function performs a GET request.
public String Get() throws IOException {
    Request request = new Request.Builder()
            .url(url).get()
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Criar uma função para analisar a resposta

Esta função simples analisa e prettifica a resposta JSON do serviço De Texto tradutor.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é fazer um pedido e obter uma resposta. Adicione estas linhas ao seu projeto:

```java
public static void main(String[] args) {
    try {
        GetLanguages getLanguagesRequest = new GetLanguages();
        String response = getLanguagesRequest.Get();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até à raiz do seu diretório de trabalho e corra:

```console
gradle build
```

Quando a construção estiver concluída, corra:

```console
gradle run
```

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura país/região nesta [lista de línguas.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

É devolvida uma resposta com êxito em JSON, tal como apresentado no exemplo seguinte:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com a API de Texto tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
