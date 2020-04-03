---
title: Bing Custom Search Java biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 32e02d3a7c1af6d15e7f381807d80f19b94da38f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587083"
---
Começa com a biblioteca de clientes bing Custom Search para Java. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas e sem anúncios para tópicos com os seus interesses. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Utilize a biblioteca de clientes Bing Custom Search para Java para:

* Encontre os resultados da pesquisa na web, a partir da sua instância de Pesquisa Personalizada Bing. 

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Biblioteca Código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefacto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* A [ferramenta de construção Gradle](https://gradle.org/install/), ou outro gestor de dependência.
* Uma instância de pesquisa personalizada bing. Consulte [Quickstart: Crie a sua primeira instância](../../quick-start.md) de Pesquisa Personalizada Bing para mais informações.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Depois de obter uma chave da sua subscrição ou recurso `AZURE_BING_CUSTOM_SEARCH_API_KEY`experimental, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada .

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

> [!TIP]
> Se não estiver a usar o Gradle, pode encontrar os detalhes da biblioteca de clientes para outros gestores de dependência no [Repositório Central de Maven.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```

Mande `gradle init` o comando do seu diretório de trabalho. Este comando cria ficheiros essenciais para a gradle, incluindo um ficheiro *build.gradle.kts* que é usado no tempo de execução para configurar a sua aplicação.

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

## <a name="install-the-client-library"></a>Instale a biblioteca do cliente 

Localize *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copiar nesta configuração de construção. Certifique-se de incluir `dependencies`a biblioteca de clientes em:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Crie uma pasta para a sua aplicação de amostras. A partir do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *BingCustomSearchSample.java*. Abra-o e `import` adicione as seguintes declarações:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Criar uma classe chamada`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Na aula, crie um `main` método e uma variável para a chave do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, feche e reabra o editor, IDE, ou shell executá-lo para aceder à variável. Definirá os métodos mais tarde.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modelo de objeto

O cliente Bing Custom Search é um objeto [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) que é criado a partir do método [autenticado do](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) objeto [BingCustomSearchManager()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) Pode enviar um pedido de pesquisa utilizando o método [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente.

A resposta da API é um objeto [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) contendo informações sobre a consulta de pesquisa e resultados de pesquisa.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Bing Custom Search para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Obtenha resultados de pesquisa a partir da sua instância de pesquisa personalizada](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O seu método principal deve incluir um objeto [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) que pegue a sua chave e a chame `authenticate()`de .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Obtenha resultados de pesquisa a partir da sua instância de pesquisa personalizada

Utilize a função [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente para enviar uma consulta de pesquisa para a sua instância personalizada. Detete o ID de `withCustomConfig` configuração personalizado ou predefinido para `1`. Depois de obter uma resposta da API, verifique se foram encontrados resultados de pesquisa. Em caso afirmativo, obtenha o primeiro resultado `webPages().value().get()` de pesquisa, ligando para a função da resposta e imprima o nome do resultado e URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Executar a aplicação

Construa a app com o seguinte comando do diretório principal do seu projeto:

```console
gradle build
```

Executar a aplicação com o `run` objetivo:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](../../tutorials/custom-search-web-page.md)
