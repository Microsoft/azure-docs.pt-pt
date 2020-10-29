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
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 6f5d1fd8a179f88677ddd6d7b1875f60836ade51
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924997"
---
Começa com a biblioteca de clientes Azure Content Moderador para Java. Siga estes passos para instalar o pacote Maven e experimente o código de exemplo para tarefas básicas. 

Content Moderator é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Utilize o serviço de moderação de conteúdo movido a IA para digitalizar texto, imagem e vídeos e aplicar automaticamente as bandeiras de conteúdo. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Utilize a biblioteca de clientes Content Moderador para:

* Imagens moderadas
* Texto moderado

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Artefacto (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Amostras](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" crie um recurso De Moderador de Conteúdo crie um recurso De Moderador de Conteúdo no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao Moderador de Conteúdo. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts,* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin** .

## <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Encontre *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copie na seguinte configuração de construção. Esta configuração define o projeto como uma aplicação Java cujo ponto de entrada é a classe **ContentModeratorQuickstart** . Importa a biblioteca de clientes content moderador, bem como o SDK GSON para a serialização JSON.

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

### <a name="create-a-java-file"></a>Criar um ficheiro Java


A partir do seu diretório de trabalho, executar o seguinte comando para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *ContentModeratorQuickstart.java* . Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)que contém os exemplos de código neste arranque rápido.

Na classe **ContentModeratorQuickstart** da aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso [nome do produto] que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes** . Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vai definir isto mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características da biblioteca de clientes Content Moderador Java.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Esta classe é necessária para todas as funcionalidades do Moderador de Conteúdo. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|
|[Imagem Dedesederação](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Esta classe fornece a funcionalidade de análise de imagens para conteúdo adulto, informações pessoais ou rostos humanos.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Esta classe fornece a funcionalidade de análise de texto para linguagem, profanação, erros e informações pessoais.|
|[Revisões](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Esta classe fornece a funcionalidade das APIs de revisão, incluindo os métodos para criar empregos, fluxos de trabalho personalizados e avaliações humanas.|


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes do Content Moderador para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Imagens moderadas](#moderate-images)
* [Texto moderado](#moderate-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

No método da `main` aplicação, crie um objeto [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) utilizando o valor final da subscrição e a chave de subscrição.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Imagens moderadas

### <a name="set-up-sample-image"></a>Configurar a imagem da amostra

Num novo método, construa um objeto **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel?view=azure-java-stable)** com uma determinada cadeia URL que aponta para uma imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definir classe de ajudante

Em seguida, no seu ficheiro *ContentModeratorQuickstart.java,* adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart.** Esta classe interior é usada no processo de moderação da imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


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

Após o `while` loop, adicione o seguinte código, que imprime os resultados na consola e num ficheiro de saída, *src/main/resources/ModerationOutput.jsem* .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a `try` declaração e adicione uma declaração para completar o `catch` método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="moderate-text"></a>Texto moderado

### <a name="set-up-sample-text"></a>Configurar texto de amostra

No topo da sua classe **ContentModeratorQuickstart,** defina uma referência a um ficheiro de texto local. Adicione um ficheiro .txt ao seu diretório de projeto e insira o texto que gostaria de analisar.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analisar texto

Crie um novo método que leia o ficheiro .txt e ligue para o método **screenText** em cada linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Imprima resultados de moderação de texto

Adicione o seguinte código para imprimir os resultados da moderação a um ficheiro .json no seu diretório de projeto.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Feche a `try` `catch` declaração para completar o método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


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
> [Conceitos de moderação de imagem](../../image-moderation-api.md)

* [O que é o Content Moderator do Azure?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).