---
title: Bing Visual Search Java biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: d1c9589265726bae01b86c152853c40f79825ceb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947172"
---
Utilize este quickstart para começar a obter insights de imagem do serviço de Pesquisa Visual Bing, utilizando a biblioteca do cliente Java. Embora a Bing Visual Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte para este arranque rápido pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Utilize a biblioteca de clientes Bing Visual Search para Java para:

* Faça upload de uma imagem para enviar um pedido de pesquisa visual.
* Obtenha o token de imagem e as etiquetas de pesquisa visual.

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  [Artefacto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Depois de obter uma chave do seu recurso, [crie uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave, chamada `BING_SEARCH_V7_SUBSCRIPTION_KEY` .

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

Localize *build.gradle.kts* e abra-o com o seu IDE ou editor de texto preferido. Em seguida, copiar nesta configuração de construção:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Crie uma pasta para a sua aplicação de amostra. Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Crie uma pasta para a imagem que pretende enviar para a API. Coloque a imagem dentro da pasta **de recursos.**

```console
mkdir -p src/main/resources
``` 

Navegue para a nova pasta e crie um ficheiro chamado *BingVisualSearchSample.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Então crie uma nova classe.

```java
public class BingVisualSearchSample {
}
```

No método da `main` aplicação, crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável. Em seguida, crie uma `byte[]` para a imagem que vai carregar. Crie um `try` bloco para os métodos que definirá mais tarde, carregue a imagem e converta-a em bytes usando `toByteArray()` .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Pode encontrar a biblioteca do cliente e informações para outros gestores de dependência no [Repositório Central de Maven.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

No ficheiro *build.gradle.kts* do seu projeto, certifique-se de incluir a biblioteca do cliente como `implementation` declaração. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Bing Visual Search e Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar um pedido de pesquisa visual](#send-a-visual-search-request)
* [Imprima as etiquetas de token de visão e de pesquisa visual](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart pressupõe que [criou uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave Bing Visual Search, chamada `BING_SEARCH_V7_SUBSCRIPTION_KEY` .


No seu método principal, certifique-se de usar a sua chave de subscrição para instantaneaizar um objeto [BingVisualSearchAPI.](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Enviar um pedido de pesquisa visual

Num novo método, envie o conjunto byte de imagem (que foi criado no `main()` método) utilizando o método [bingImages ().visualSearch do cliente().](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Imprima as etiquetas de token de visão e de pesquisa visual

Verifique se o objeto [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge) é nulo. Caso contrário, imprima o token de insights de imagem, o número de tags, o número de ações e o primeiro tipo de ação.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar a aplicação com o `run` objetivo:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-visual-search-single-page-app.md)
