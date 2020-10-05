---
title: Início rápido da biblioteca de clientes Java moderador de conteúdo
titleSuffix: Azure Cognitive Services
description: Neste quickstart, aprenda a começar com a biblioteca de clientes Azure Content Moderador para Java. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 1e32cd924c8e0f713ebe7cedfca0466a1e07c3bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332584"
---
Começa com a biblioteca de clientes Azure Content Moderador para Java. Siga estes passos para instalar o pacote Maven e experimente o código de exemplo para tarefas básicas. 

Content Moderator é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Utilize o serviço de moderação de conteúdo movido a IA para digitalizar texto, imagem e vídeos e aplicar automaticamente as bandeiras de conteúdo. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Utilize a biblioteca de clientes Content Moderador para:

* Imagens moderadas para conteúdo adulto ou picante, texto ou rostos humanos.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable)  |  [Artefacto (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.

## <a name="create-a-content-moderator-resource"></a>Criar um recurso de Moderador de Conteúdo

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para Moderador de Conteúdo utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [O CLI Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Veja o seu recurso no [portal Azure](https://portal.azure.com/).

Depois de obter uma chave do seu recurso, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada `AZURE_CONTENTMODERATOR_KEY` .

## <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```
Execute `gradle init`. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts,* que é usado em tempo de execução para criar e configurar a sua aplicação. Executar este comando a partir do seu diretório de trabalho:

```console
gradle init --type basic
```

Quando solicitado para escolher um script de construção DSL, selecione **Kotlin**.

## <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Encontre *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copie na seguinte configuração de construção. Esta configuração define o projeto como uma aplicação Java cujo ponto de entrada é a classe **ContentModeratorQuickstart**. Importa a biblioteca de clientes content moderador, bem como o Gson sdk para a serialização JSON.

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

A partir do seu diretório de trabalho, executar o seguinte comando para criar uma pasta de origem do projeto.

```console
mkdir -p src/main/java
```

Em seguida, crie um ficheiro chamado *ContentModeratorQuickstart.java* na nova pasta. Abra o ficheiro no seu editor preferido ou IDE e importe as seguintes bibliotecas no topo:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Content Moderador Java.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Esta classe é necessária para todas as funcionalidades do Moderador de Conteúdo. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|
|[Imagem Dedesederação](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Esta classe fornece a funcionalidade de análise de imagens para conteúdo adulto, informações pessoais ou rostos humanos.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Esta classe fornece a funcionalidade de análise de texto para linguagem, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e avaliações humanas.|


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes do Content Moderador para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Imagens moderadas](#moderate-images)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este passo pressupõe que [criou uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a sua chave de Moderador de Conteúdo, denominada `AZURE_CONTENTMODERATOR_KEY` .

No método da `main` aplicação, crie um objeto [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) utilizando o valor final da subscrição e variável de ambiente chave de subscrição. 

> [!NOTE]
> Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Imagens moderadas

### <a name="get-sample-images"></a>Obtenha imagens de amostra

Na **pasta src/main/do** seu projeto, crie uma pasta de **recursos** e navegue até ele. Em seguida, crie um novo ficheiro de texto, *ImageFiles.txt*. Neste ficheiro, adiciona-se os URLs de imagens para analisar &mdash; um URL em cada linha. Pode utilizar as seguintes imagens de amostra:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir classe de ajudante

Em seguida, no seu ficheiro *ContentModeratorQuickstart.java,* adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart.** Esta classe interior será usada mais tarde no processo de moderação de imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterar através de imagens

Em seguida, adicione o seguinte código à parte inferior do `main` método. Ou pode adicioná-lo a um método separado que é chamado de `main` . Este código passa por cada linha do ficheiro _ImageFiles.txt._

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="analyze-content"></a>Analisar conteúdo
Esta linha de código verifica a imagem no URL dado para conteúdo adulto ou picante. Consulte o guia de moderação da imagem para obter informações sobre estes termos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verifique se há texto
Esta linha de código verifica a imagem por texto visível.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verifique se há rostos
Esta linha de código verifica a imagem dos rostos humanos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Finalmente, guarde a informação devolvida na `EvaluationData` lista.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimir resultados

Após o `while` loop, adicione o seguinte código, que imprime os resultados na consola e num ficheiro de saída, *src/main/resources/ModerationOutput.jsem*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a `try` declaração e adicione uma declaração para completar o `catch` método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar a aplicação com o `gradle run` comando:

```console
gradle run
```

Em seguida, navegue para o *src/main/resources/ModerationOutput.jsem* arquivo e veja os resultados da moderação do seu conteúdo.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Content Moderador Java para executar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação, lendo um guia conceptual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).