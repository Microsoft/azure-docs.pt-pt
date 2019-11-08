---
title: 'Início rápido: biblioteca de cliente Content Moderator para Java'
titleSuffix: Azure Cognitive Services
description: Saiba como começar a usar os serviços cognitivas do Azure Content Moderator biblioteca de cliente para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: edc51be93ba209a1c60970e6fa1b47fca75048c6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744451"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Início rápido: biblioteca de cliente Content Moderator para Java

Introdução à biblioteca de cliente do Content Moderator para Java. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Content Moderator é um serviço cognitiva que verifica o conteúdo de texto, imagem e vídeo para materiais potencialmente ofensivos, arriscados ou indesejáveis. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores.

Use a biblioteca de cliente do Content Moderator para Java para:

* Imagens moderadas para conteúdo adulto ou erótico, texto ou faces humanas.

[Exemplos](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator) de | [do Maven ( | do artefato) de documentação de](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) [referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de compilação gradle](https://gradle.org/install/)ou outro gerenciador de dependência.

## <a name="setting-up"></a>Configurando

### <a name="create-a-content-moderator-azure-resource"></a>Criar um Content Moderator recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Content Moderator usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave chamada `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto gradle

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```
Execute `gradle init`. Este comando criará arquivos de Build essenciais para gradle, incluindo *Build. gradle. KTs*, que é usado em tempo de execução para criar e configurar seu aplicativo. Execute este comando em seu diretório de trabalho:

```console
gradle init --type basic
```

Quando for solicitado a escolher um script de compilação DSL, selecione **Kotlin**.

Localize *Build. gradle. KTs* e abra-o com seu IDE ou editor de texto preferencial. Em seguida, copie a seguinte configuração de compilação. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ContentModeratorQuickstart**. Ele importa o SDK do Content Moderator, bem como o SDK do Gson para serialização JSON.

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

Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto.

```console
mkdir -p src/main/java
```

Em seguida, crie um arquivo chamado *ContentModeratorQuickstart. java* na nova pasta. Abra o arquivo em seu editor preferido ou IDE e importe as seguintes bibliotecas na parte superior:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos do SDK do Java Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Essa classe é necessária para todas as funcionalidades de Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo adulto, informações pessoais ou faces humanas.|
|[Moderações de](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Revisões](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Essa classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|


## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Content Moderator para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Imagens moderadas](#moderate-images)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Esta etapa pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de Content moderator, chamada `AZURE_CONTENTMODERATOR_KEY`.

No método `main` do aplicativo, crie um objeto [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) usando o valor do ponto de extremidade da assinatura e a variável de ambiente da chave de assinatura. 

> [!NOTE]
> Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Imagens moderadas

### <a name="get-images"></a>Obter imagens

Na pasta **src/main/** do seu projeto, crie uma pasta de **recursos** e navegue até ela. Em seguida, crie um novo arquivo de texto, *ImageFiles. txt*. Nesse arquivo, você adiciona as URLs de imagens para analisar&mdash;uma URL em cada linha. Você pode usar as seguintes imagens de exemplo:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir classe auxiliar

Em seguida, no arquivo *ContentModeratorQuickstart. java* , adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart** . Essa classe interna será usada posteriormente no processo de moderação da imagem.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterar por meio de imagens

Em seguida, adicione o código a seguir à parte inferior do método `main`. Ou você pode adicioná-lo a um método separado que é chamado a partir de `main`. Esse código percorre cada linha do arquivo _ImageFiles. txt_ .

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificar conteúdo adulto/erótico
Essa linha de código verifica a imagem na URL fornecida para conteúdo adulto ou erótico. Consulte o guia conceitual de moderação de imagem para obter informações sobre esses termos.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verificar texto
Essa linha de código verifica a imagem em busca de texto visível.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificar se há faces
Essa linha de código verifica a imagem para obter faces humanas.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Por fim, armazene as informações retornadas na lista de `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimir resultados

Após o loop de `while`, adicione o código a seguir, que imprime os resultados no console e em um arquivo de saída, *src/main/resources/ModerationOutput. JSON*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a instrução `try` e adicione uma instrução `catch` para concluir o método.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Executar a aplicação

Você pode compilar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

Em seguida, navegue até o arquivo *src/main/resources/ModerationOutput. JSON* e exiba os resultados da moderação do conteúdo.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar a biblioteca Java Content Moderator para executar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Azure Content Moderator?](./overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).