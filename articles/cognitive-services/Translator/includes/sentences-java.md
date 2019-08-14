---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a6c12a2fdc8616dd6f7107d11e8f6c77401811fb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968574"
---
## <a name="prerequisites"></a>Pré-requisitos

* [JDK 7 ou posterior](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com gradle

Vamos começar criando um diretório de trabalho para este projeto. Na linha de comando (ou terminal), execute este comando:

```console
mkdir length-sentence-sample
cd length-sentence-sample
```

Em seguida, você vai inicializar um projeto gradle. Esse comando criará arquivos de Build essenciais para gradle, o mais importante `build.gradle.kts`, o, que é usado em tempo de execução para criar e configurar seu aplicativo. Execute este comando em seu diretório de trabalho:

```console
gradle init --type basic
```

Quando for solicitado a escolher uma **DSL**, selecione **Kotlin**.

## <a name="configure-the-build-file"></a>Configurar o arquivo de compilação

Localize `build.gradle.kts` -o e abra-o com seu IDE ou editor de texto favorito. Em seguida, copie nesta configuração de compilação:

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

Observe que esse exemplo tem dependências de OkHttp para solicitações HTTP e Gson para manipular e analisar JSON. Se você quiser saber mais sobre as configurações de compilação, consulte [criando novas compilações do gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Crie um ficheiro de Java

Vamos criar uma pasta para seu aplicativo de exemplo. Em seu diretório de trabalho, execute:

```console
mkdir -p src/main/java
```

Em seguida, nesta pasta, crie um arquivo chamado `LengthSentence.java`.

## <a name="import-required-libraries"></a>Importar bibliotecas necessárias

Abra `LengthSentence.java` e adicione estas instruções de importação:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definir variáveis

Primeiro, você precisará criar uma classe pública para seu projeto:

```java
public class LengthSentence {
  // All project code goes here...
}
```

Adicione essas linhas à `LengthSentence` classe. Você observará que, junto com `api-version`o, você pode definir o idioma de entrada. Neste exemplo, é o inglês.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```
Se você estiver usando uma assinatura de vários serviços cognitivas, também deverá incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre como autenticar com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e criar uma solicitação

Adicione essa linha à `LengthSentence` classe para instanciar o: `OkHttpClient`

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos criar a solicitação POST. Sinta-se à vontade para alterar o texto. O texto deve ter escape.

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

Essa função simples analisa e prettifies a resposta JSON do serviço Tradução de Texto.

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

A última etapa é fazer uma solicitação e obter uma resposta. Adicione estas linhas ao seu projeto:

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

É isso, você está pronto para executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até a raiz do seu diretório de trabalho e execute:

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

Dê uma olhada na referência da API para entender tudo o que você pode fazer com o API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
