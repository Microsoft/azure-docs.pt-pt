---
title: 'Início rápido: Obter comprimentos de sentença, Java - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como determinar o comprimento de sentença com Java e a API de texto do Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: d7679f41b5e1c1fe6705ef3ed4be28a521f0d0d2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728493"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>Início rápido: Utilize a API de texto do Translator para determinar o comprimento de sentença com Java

Neste início rápido, irá aprender como determinar os comprimentos de sentença com Java e a API de texto do Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* [JDK 7 ou posterior](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com o Gradle

Vamos começar por criar um diretório de trabalho para esse projeto. A partir da linha de comandos (ou terminal), execute este comando:

```console
mkdir break-sentence-sample
cd break-sentence-sample
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
    mainClassName = "BreakSentence"
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
mkdir -p src/main/java
```

Em seguida, nesta pasta, crie um ficheiro denominado `BreakSentence.java`.

## <a name="import-required-libraries"></a>Importar as bibliotecas necessárias

Abra `BreakSentence.java` e adicioná-las importar instruções:

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
public class BreakSentence {
  // All project code goes here...
}
```

Adicionar estas linhas para o `BreakSentence` classe. Perceberá que, juntamente com o `api-version`, pode definir o idioma de entrada. Neste exemplo é em inglês.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e criar um pedido

Adicionar esta linha para o `BreakSentence` classe para instanciar o `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos criar o pedido POST. Pode alterar o texto. Deve ser escrito o texto.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
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
        BreakSentence breakSentenceRequest = new BreakSentence();
        String response = breakSentenceRequest.Post();
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

É devolvida uma resposta com êxito em JSON, tal como apresentado no exemplo seguinte:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Passos Seguintes

Explore o código de exemplo neste início rápido e noutros, incluindo a tradução e a transliteração, assim como outros exemplos de projetos de Tradução de Texto no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Consulte também

* [Traduzir texto](quickstart-java-translate.md)
* [Transliterar texto](quickstart-java-transliterate.md)
* [Identificar o idioma por entrada](quickstart-java-detect.md)
* [Obter traduções alternativas](quickstart-java-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-java-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-java-sentences.md)
