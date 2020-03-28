---
title: 'Quickstart: Biblioteca de clientes bing visual search para Java Microsoft Docs'
description: Faça upload de uma imagem usando o Bing Visual Search SDK e obtenha informações sobre isso.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379504"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Quickstart: Biblioteca de clientes bing visual search para Java

Começa com a biblioteca de clientes bing visual search para Java. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. 

Utilize a biblioteca de clientes bing visual search para Java para:

* Faça upload de uma imagem para enviar um pedido de pesquisa visual.
* Obtenha o símbolo da imagem e etiquetas de pesquisa visual.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Biblioteca Código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefacto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle](https://gradle.org/install/), ou outro gestor de dependência

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Depois de obter uma chave da sua subscrição ou recurso `BING_SEARCH_V7_SUBSCRIPTION_KEY`experimental, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada .

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```

Mande `gradle init` o comando do seu diretório de trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado no tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

Localize *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copiar nesta configuração de construção:

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

Crie uma pasta para a sua aplicação de amostras. A partir do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Crie uma pasta para a imagem que pretende enviar para a API. Coloque a imagem dentro da pasta de **recursos.**

```console
mkdir -p src/main/resources
``` 

Navegue para a nova pasta e crie um ficheiro chamado *BingVisualSearchSample.java*. Abra-o no seu editor preferido ou `import` IDE e adicione as seguintes declarações:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Então crie uma nova classe.

```java
public class BingVisualSearchSample {
}
```

No método da `main` aplicação, crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável. Em seguida, crie um `byte[]` para a imagem que vai carregar. Crie `try` um bloco para os métodos que definirá mais tarde, `toByteArray()`e carregue a imagem e converta-a em bytes usando .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Você pode encontrar a biblioteca de clientes e informações para outros gestores de dependência no [Repositório Central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

No ficheiro *build.gradle.kts* do seu projeto, não se `implementation` esqueça de incluir a biblioteca de clientes como um comunicado. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes bing visual search e Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar um pedido de pesquisa visual](#send-a-visual-search-request)
* [Imprima o token de imagem e as etiquetas de pesquisa visual](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `BING_SEARCH_V7_SUBSCRIPTION_KEY`a sua chave Bing Visual Search, chamada .


No seu método principal, certifique-se de usar a sua chave de subscrição para instantaneamente um objeto [BingVisualSearchAPI.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Enviar um pedido de pesquisa visual

Num novo método, envie a matriz de byte de imagem (que foi criada no `main()` método) utilizando o método [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) do cliente. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Imprima o token de imagem e as etiquetas de pesquisa visual

Verifique se o objeto [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) é nulo. Caso contrário, imprima o símbolo da imagem, o número de tags, o número de ações e o primeiro tipo de ação.

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

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)
