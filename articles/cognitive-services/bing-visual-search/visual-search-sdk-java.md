---
title: 'Início rápido: Biblioteca de cliente Pesquisa Visual do Bing para Java | Microsoft Docs'
description: Carregue uma imagem usando o SDK do Pesquisa Visual do Bing e obtenha informações sobre ele.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695863"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Início rápido: Biblioteca de cliente Pesquisa Visual do Bing para Java

Introdução à biblioteca de cliente do Pesquisa Visual do Bing para Java. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. 

Use a biblioteca de cliente do Pesquisa Visual do Bing para Java para:

* Carregue uma imagem para enviar uma solicitação de pesquisa visual.
* Obtenha o token do insight de imagem e as marcas de pesquisa visual.

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) de artefato do | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)[de documentação de referência (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de compilação gradle](https://gradle.org/install/)ou outro gerenciador de dependências

## <a name="setting-up"></a>Configurando

### <a name="create-a-bing-visual-search-azure-resource"></a>Criar um Pesquisa Visual do Bing recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual do Bing usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave `BING_SEARCH_V7_SUBSCRIPTION_KEY`, denominada.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto gradle

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `gradle init` comando do seu diretório de trabalho. Este comando criará arquivos de Build essenciais para gradle, incluindo *Build. gradle. KTs* , que é usado em tempo de execução para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando for solicitado a escolher uma **DSL**, selecione **Kotlin**.

Localize *Build. gradle. KTs* e abra-o com seu IDE ou editor de texto preferencial. Em seguida, copie nesta configuração de compilação:

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

Crie uma pasta para o aplicativo de exemplo. Em seu diretório de trabalho, execute o seguinte comando:

```console
mkdir -p src/main/java
```

Crie uma pasta para a imagem que você deseja carregar na API. Coloque a imagem dentro da pasta **recursos** .

```console
mkdir -p src/main/resources
``` 

Navegue até a nova pasta e crie um arquivo chamado *BingVisualSearchSample. java*. Abra-o em seu editor ou IDE preferido e adicione as `import` seguintes instruções:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Em seguida, crie uma nova classe.

```java
public class BingVisualSearchSample {
}
```

No método `main` do aplicativo, crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável. Em seguida, crie um `byte[]` para a imagem que você carregará. Crie um bloco `try` para os métodos que você definirá posteriormente, e carregue a imagem e converta-a em bytes usando `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Este guia de início rápido usa o gradle Dependency Manager. Você pode encontrar a biblioteca de cliente e informações para outros gerenciadores de dependência no [repositório central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

No arquivo *Build. gradle. KTs* do seu projeto, certifique-se de incluir a biblioteca de cliente como uma instrução `implementation`. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Visual do Bing e Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar uma solicitação de pesquisa Visual](#send-a-visual-search-request)
* [Imprimir o token do Image Insight e as marcas de pesquisa Visual](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de pesquisa visual do Bing, chamada `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Em seu método principal, certifique-se de usar sua chave de assinatura para criar uma instância de um objeto [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) .

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Enviar uma solicitação de pesquisa Visual

Em um novo método, envie a matriz de bytes de imagem (que foi criada no método `main()`) usando o método [bingImages (). visualSearch ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) do cliente. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Imprimir o token do Image Insight e as marcas de pesquisa Visual

Verifique se o objeto [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) é nulo. Caso contrário, imprima o token de informações de imagem, o número de marcas, o número de ações e o primeiro tipo de ação.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Executar a aplicação

Você pode compilar o aplicativo com:

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
> [Criar um aplicativo Web de página única](tutorial-bing-visual-search-single-page-app.md)
