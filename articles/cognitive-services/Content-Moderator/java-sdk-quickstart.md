---
title: 'Quickstart: Biblioteca de clientes moderador de conteúdo para Java'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenda a começar com a biblioteca de clientes do Moderador de Conteúdo de Serviços Cognitivos Azure para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5dd1c1bb2b321e617efc430ce48745c06a827305
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772450"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Quickstart: Biblioteca de clientes moderador de conteúdo para Java

Começar com a biblioteca de clientes moderador de conteúdo para Java. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitivo que verifica o conteúdo de texto, imagem e vídeo para material potencialmente ofensivo, arriscado ou de outra forma indesejável. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores.

Utilize a biblioteca de clientes Moderador de Conteúdo para Java para:

* Imagens moderadas para conteúdo adulto ou picante, texto ou rostos humanos.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator) [de artefacto (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle](https://gradle.org/install/), ou outro gestor de dependência.

## <a name="setting-up"></a>Configuração

### <a name="create-a-content-moderator-azure-resource"></a>Criar um recurso Azure moderador de conteúdo

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para moderador de conteúdo utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso `AZURE_CONTENTMODERATOR_KEY`experimental, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada .

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```
Execute `gradle init`. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts*, que é usado no tempo de execução para criar e configurar a sua aplicação. Execute este comando a partir do seu diretório de trabalho:

```console
gradle init --type basic
```

Quando for solicitado a escolher um script de construção DSL, selecione **Kotlin**.

Encontre *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copie na seguinte configuração de construção. Esta configuração define o projeto como uma aplicação Java cujo ponto de entrada é a classe **ContentModeratorQuickstart**. Importa o SDK moderador de conteúdo, bem como o sdk Gson para a serialização da JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

A partir do seu diretório de trabalho, execute o seguinte comando para criar uma pasta de origem do projeto.

```console
mkdir -p src/main/java
```

Em seguida, crie um ficheiro chamado *ContentModeratorQuickstart.java* na nova pasta. Abra o ficheiro no seu editor ou IDE preferido e importe as seguintes bibliotecas no topo:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características do Moderador de Conteúdo Java SDK.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Esta aula é necessária para todas as funcionalidades moderadoras de conteúdo. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes.|
|[Imagem Moderação](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Esta aula fornece a funcionalidade para analisar imagens para conteúdos para adultos, informações pessoais ou rostos humanos.|
|[Moderação de texto](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Esta aula fornece a funcionalidade para analisar texto para linguagem, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e revisões humanas.|


## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Moderador de Conteúdo para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Imagens moderadas](#moderate-images)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este passo pressupõe que [criou uma variável](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) ambiental `AZURE_CONTENTMODERATOR_KEY`para a sua chave Moderador de Conteúdo, nomeada .

No método da `main` aplicação, crie um objeto [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) utilizando o valor final da subscrição e a variável de ambiente chave de subscrição. 

> [!NOTE]
> Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Imagens moderadas

### <a name="get-images"></a>Obter imagens

Na **pasta src/main/pasta** do seu projeto, crie uma pasta de **recursos** e navegue até ele. Em seguida, crie um novo ficheiro de texto, *ImageFiles.txt*. Neste ficheiro, adiciona os URLs de&mdash;imagens para analisar um URL em cada linha. Pode utilizar as seguintes imagens da amostra:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir classe de ajudante

Em seguida, no seu ficheiro *ContentModeratorQuickstart.java,* adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart.** Esta classe interior será usada mais tarde no processo de moderação da imagem.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterado através de imagens

Em seguida, adicione o seguinte `main` código na parte inferior do método. Ou, pode adicioná-lo a um método `main`separado que é chamado de . Este código passa por cada linha do ficheiro _ImageFiles.txt._

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verifique se há conteúdo adulto/picante
Esta linha de código verifica a imagem no URL dado para conteúdo adulto ou picante. Consulte o guia conceptual de moderação da imagem para obter informações sobre estes termos.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verifique se há texto
Esta linha de código verifica a imagem para texto visível.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verifique se há rostos
Esta linha de código verifica a imagem de rostos humanos.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Finalmente, guarde as `EvaluationData` informações devolvidas na lista.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimir resultados

Depois `while` do loop, adicione o seguinte código, que imprime os resultados para a consola e para um ficheiro de saída, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a `try` declaração e `catch` adicione uma declaração para completar o método.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar o pedido `gradle run` com o comando:

```console
gradle run
```

Em seguida, navegue para o ficheiro *sRC/main/resources/ModerationOutput.json* e veja os resultados da sua moderação de conteúdo.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Java moderador de conteúdo para executar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação através da leitura de um guia conceptual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Content Moderator do Azure?](./overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).