---
title: Bing Custom Search Java biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948494"
---
Começa com a biblioteca de clientes Bing Custom Search para Java. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas e sem anúncios para tópicos que lhe interessam. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Utilize a biblioteca de clientes Bing Custom Search para Java para:

* Encontre os resultados da pesquisa na web, a partir da sua instância de Pesquisa Personalizada Bing.

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Artefacto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/).
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Um caso de pesquisa personalizada Bing. Consulte [Quickstart: Crie a sua primeira instância de pesquisa personalizada Bing](../../quick-start.md) para obter mais informações.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Depois de obter uma chave do seu recurso, [crie uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave, chamada `AZURE_BING_CUSTOM_SEARCH_API_KEY` .

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

> [!TIP]
> Se não estiver a utilizar o Gradle, pode encontrar os dados da biblioteca do cliente para outros gestores de dependência no [Repositório Central de Maven.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando cria ficheiros de construção essenciais para Gradle, incluindo um ficheiro *build.gradle.kts* que é usado em tempo de execução para configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

## <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Localize *build.gradle.kts* e abra-o com o seu IDE ou editor de texto preferido. Em seguida, copie nesta configuração de construção. Certifique-se de incluir a biblioteca do cliente em `dependencies` :

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

Crie uma pasta para a sua aplicação de amostra. Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *BingCustomSearchSample.java*. Abra-o e adicione as `import` seguintes declarações:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Criar uma classe chamada `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Na classe, crie um `main` método e uma variável para a chave do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, feche e reabra o editor, IDE, ou shell executando-a para aceder à variável. Definirá os métodos mais tarde.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modelo de objeto

O cliente Bing Custom Search é um objeto [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) que é criado a partir do método [autenticado](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) do objeto [BingCustomSearchManager.).](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager) Pode enviar um pedido de pesquisa utilizando o método [BingCustomInstances.search() do](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) cliente.

A resposta API é um objeto [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) que contém informações sobre a consulta de pesquisa e resultados de pesquisa.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Bing Custom Search para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Obtenha resultados de pesquisa a partir do seu caso de pesquisa personalizado](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O seu método principal deve incluir um objeto [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) que leva a sua chave, e chama-lhe `authenticate()` .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Obtenha resultados de pesquisa a partir do seu caso de pesquisa personalizado

Utilize a função [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente para enviar uma consulta de pesquisa para a sua instância personalizada. Desa estade o `withCustomConfig` ID de configuração personalizado ou o padrão para `1` . Depois de obter uma resposta da API, verifique se foram encontrados resultados de pesquisa. Em caso afirmativo, obtenha o primeiro resultado de pesquisa chamando a função da resposta `webPages().value().get()` e imprima o nome do resultado e URL.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Executar a aplicação

Construa a aplicação com o seguinte comando a partir do diretório principal do seu projeto:

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
> [Construa um aplicativo web de pesquisa personalizada](../../tutorials/custom-search-web-page.md)
