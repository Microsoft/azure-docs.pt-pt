---
title: 'Início rápido: Converter o script de texto, Java - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, vai aprender a transcrição de texto (converter) a partir de um script para outro e usando Java e a API de REST de texto do Translator. Neste exemplo, o japonês é transliterado para utilizar o alfabeto latino.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 58821492c6972eeef622f8dc8a777f8da70acdf1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895928"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Início rápido: Utilizar a API de texto do Translator a transcrição de texto com Java

Neste início rápido, vai aprender a transcrição de texto (converter) a partir de um script para outro e usando Java e a API de REST de texto do Translator. No exemplo apresentado, o japonês é transliterado para utilizar o alfabeto latino.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK 7 ou posterior](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com o Gradle

Vamos começar por criar um diretório de trabalho para esse projeto. A partir da linha de comandos (ou terminal), execute este comando:

```console
mkdir transliterate-sample
cd transliterate-sample
```

Em seguida, vamos inicializar um projeto do Gradle. Este comando cria ficheiros de construção essenciais para Gradle, mais importante, o `build.gradle.kts`, que é utilizado no tempo de execução para criar e configurar a sua aplicação. Execute este comando a partir do diretório de trabalho:

```console
gradle init --type basic
```

Quando lhe for pedido para escolher uma **DSL**, selecione **Kotlin**.

## <a name="configure-the-build-file"></a>Configure o arquivo de compilação

Localize `build.gradle.kts` e abra-o com o seu editor de texto ou IDE preferido. Em seguida, copie nesta configuração de compilação:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Observe que este exemplo tem dependências em OkHttp pedidos de HTTP e Gson processar e analisar JSON. Se gostaria de saber mais sobre as configurações de compilação, veja [criando novas compilações do Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Crie um ficheiro de Java

Vamos criar uma pasta para a sua aplicação de exemplo. A partir do seu diretório de trabalho, execute:

```console
mkdir -p src\main\java
```

Em seguida, nesta pasta, crie um ficheiro denominado `Transliterate.java`.

## <a name="import-required-libraries"></a>Importar as bibliotecas necessárias

Abra `Transliterate.java` e adicioná-las importar instruções:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definir variáveis

Em primeiro lugar, terá de criar uma classe pública para o seu projeto:

```java
public class Transliterate {
  // All project code goes here...
}
```

Adicionar estas linhas para o `Transliterate` classe. Perceberá que, juntamente com o `api-version`, foi anexou dois parâmetros adicionais para o `url`. Esses parâmetros são usados para definir o idioma de entrada e os scripts de Transliteração. Neste exemplo, é definida como japonês (`jpan`) e latinos (`latn`). Certifique-se de que atualizar o valor da chave de subscrição.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e criar um pedido

Adicionar esta linha para o `Transliterate` classe para instanciar o `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos criar o pedido POST. Pode alterar o texto de Transliteração.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Criar uma função para analisar a resposta

Esta função simples analisa e prettifies a resposta JSON do serviço de texto do Translator.

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

A última etapa é fazer um pedido e obter uma resposta. Adicione estas linhas ao seu projeto:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar a aplicação de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para a raiz do seu diretório de trabalho e execute:

```console
gradle build
```

Quando a compilação for concluída, execute:

```console
gradle run
```

## <a name="sample-response"></a>Resposta de amostra

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Passos Seguintes

Explore o código de exemplo para este guia de início rápido e outros, incluindo a tradução e a identificação do idioma, assim como outros projetos de Tradução de Texto no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Consulte também

* [Traduzir texto](quickstart-java-translate.md)
* [Identificar o idioma por entrada](quickstart-java-detect.md)
* [Obter traduções alternativas](quickstart-java-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-java-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-java-sentences.md)
