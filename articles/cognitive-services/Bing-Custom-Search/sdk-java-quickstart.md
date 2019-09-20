---
title: 'Início rápido: Biblioteca de cliente Pesquisa Personalizada do Bing para Java | Microsoft Docs'
description: Introdução à biblioteca de cliente do Pesquisa Personalizada do Bing para Java solicitando os resultados da pesquisa de sua instância do Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 7d1a7b2cdba082c78a1753ea0dcce6ead02ab036
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147980"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Início rápido: Biblioteca de cliente Pesquisa Personalizada do Bing para Java

Introdução à biblioteca de cliente do Pesquisa Personalizada do Bing para Java. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizadas sem anúncios para tópicos sobre os quais você se preocupa.

Use a biblioteca de cliente do Pesquisa Personalizada do Bing para Java para:

* Localize os resultados da pesquisa na Web, na sua instância do Pesquisa Personalizada do Bing. 

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) de artefato do | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)[de documentação de referência (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie um gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* A [ferramenta de compilação gradle](https://gradle.org/install/)ou outro gerenciador de dependência.
* Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: Crie sua primeira instância](quick-start.md) de pesquisa personalizada do Bing para obter mais informações.

## <a name="setting-up"></a>Configurando

### <a name="create-a-bing-custom-search-azure-resource"></a>Criar um Pesquisa Personalizada do Bing recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Personalizada do Bing usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave `AZURE_BING_CUSTOM_SEARCH_API_KEY`, denominada.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto gradle

> [!TIP]
> Se você não estiver usando o gradle, poderá encontrar os detalhes da biblioteca de cliente para outros gerenciadores de dependência no [repositório central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `gradle init` comando do seu diretório de trabalho. Este comando cria arquivos de Build essenciais para gradle, incluindo um arquivo *Build. gradle. KTs* que é usado em tempo de execução para configurar seu aplicativo.

```console
gradle init --type basic
```

Quando for solicitado a escolher uma **DSL**, selecione **Kotlin**.

## <a name="install-the-client-library"></a>Instalar a biblioteca de cliente 

Localize *Build. gradle. KTs* e abra-o com seu IDE ou editor de texto preferencial. Em seguida, copie nessa configuração de compilação. Certifique-se de incluir a biblioteca de `dependencies`cliente em:

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

Crie uma pasta para o aplicativo de exemplo. Em seu diretório de trabalho, execute o seguinte comando:

```console
mkdir src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *BingCustomSearchSample. java*. Abra-o e adicione as `import` seguintes instruções:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Criar uma classe chamada`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Na classe, crie um `main` método e variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, feche e reabra o editor, IDE ou shell que o executa para acessar a variável. Você irá definir os métodos mais tarde.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modelo de objeto

O Pesquisa Personalizada do Bing Client é um objeto [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) que é criado a partir do método [Authenticate ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) do objeto [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) . Você pode enviar uma solicitação de pesquisa usando o método [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente.

A resposta da API é um objeto [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) que contém informações sobre a consulta de pesquisa e os resultados da pesquisa.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Personalizada do Bing para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Obter resultados da pesquisa de sua instância de pesquisa personalizada](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O método Main deve incluir um objeto [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) que usa sua chave e chama seu `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Obter resultados da pesquisa de sua instância de pesquisa personalizada

Use a função [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente para enviar uma consulta de pesquisa para sua instância personalizada. Defina como sua ID de configuração personalizada ou padrão como `1`. `withCustomConfig` Depois de obter uma resposta da API, verifique se foram encontrados resultados da pesquisa. Nesse caso, obtenha o primeiro resultado da pesquisa chamando a função da `webPages().value().get()` resposta e imprima o nome do resultado e a URL. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Executar a aplicação

Compile o aplicativo com o seguinte comando no diretório principal do projeto:

```console
gradle build
```

Execute o aplicativo com o `run` objetivo:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
