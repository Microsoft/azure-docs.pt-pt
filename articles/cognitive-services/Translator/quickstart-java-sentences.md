---
title: 'Início rápido: Obter comprimentos de sentença, Java - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como determinar o comprimento de sentença com Java e a API de texto do Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e47bbd4f50a1521eebc15f3f812f31699a7ddf13
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704480"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>Início rápido: Utilize a API de texto do Translator para determinar o comprimento de sentença com Java

Neste início rápido, irá aprender como determinar os comprimentos de sentença com Java e a API de texto do Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

>[!TIP]
> Se gostaria de ver todo o código de uma só vez, o código-fonte para este exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java).

## <a name="prerequisites"></a>Pré-requisitos

* [JDK 7 ou posterior](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com o Gradle

Vamos começar por criar um diretório de trabalho para esse projeto. A partir da linha de comandos (ou terminal), execute este comando:

```console
mkdir length-sentence-sample
cd length-sentence-sample
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
    mainClassName = "LengthSentence"
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

Em seguida, nesta pasta, crie um ficheiro denominado `LengthSentence.java`.

## <a name="import-required-libraries"></a>Importar as bibliotecas necessárias

Abra `LengthSentence.java` e adicioná-las importar instruções:

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
public class LengthSentence {
  // All project code goes here...
}
```

Adicionar estas linhas para o `LengthSentence` classe. Perceberá que, juntamente com o `api-version`, pode definir o idioma de entrada. Neste exemplo é em inglês.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```
Se estiver a utilizar uma subscrição de múltiplos serviço de serviços cognitivos, também tem de incluir o `Ocp-Apim-Subscription-Region` nos parâmetros do pedido. [Saiba mais sobre autenticação com a subscrição de múltiplos serviço](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e criar um pedido

Adicionar esta linha para o `LengthSentence` classe para instanciar o `OkHttpClient`:

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
        LengthSentence lengthSentenceRequest = new LengthSentence();
        String response = lengthSentenceRequest.Post();
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

Veja a referência de API para compreender tudo o que pode fazer com a API de texto do Translator.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Consulte também

* [Traduzir texto](quickstart-java-translate.md)
* [Transliterar texto](quickstart-java-transliterate.md)
* [Identificar o idioma por entrada](quickstart-java-detect.md)
* [Obter traduções alternativas](quickstart-java-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-java-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-java-sentences.md)
