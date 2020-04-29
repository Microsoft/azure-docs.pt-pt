---
title: 'Quickstart: Biblioteca de clientes computer vision para Java'
description: Neste arranque rápido, comece com a biblioteca de clientes computer vision para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272604"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [de artefacto (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle](https://gradle.org/install/), ou outro gestor de dependência.

## <a name="setting-up"></a>Configuração

### <a name="create-a-computer-vision-azure-resource"></a>Criar um recurso Computer Vision Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para a Visão Computacional utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT`chave e corda final de serviço, nomeada e, respectivamente.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```

Mande `gradle init` o comando do seu diretório de trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts*, que é usado no tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

Localize *build.gradle.kts* e abra-o com o seu IDE preferido ou editor de texto. Em seguida, copie na seguinte configuração de construção. Esta configuração define o projeto como uma aplicação Java cujo ponto de entrada é a classe **ComputerVisionQuickstarts**. Importa a biblioteca computer vision.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

A partir do seu diretório de trabalho, execute o seguinte comando para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *ComputerVisionQuickstarts.java*. Abra-o no seu editor preferido ou `import` IDE e adicione as seguintes declarações:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Em seguida, adicione uma definição de classe para **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Você pode encontrar a biblioteca de clientes e informações para outros gestores de dependência no [Repositório Central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

No ficheiro *build.gradle.kts* do seu projeto, inclua a biblioteca de clientes computer Vision como uma dependência.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision Java SDK.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Esta aula é necessária para todas as funcionalidades de Visão Computacional. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Esta classe provém do objeto do cliente e lida diretamente com todas as operações de imagem, tais como análise de imagem, deteção de texto e geração de miniaturas.|
|[Tipos de Características Visuais](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação padrão da Análise. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes computer vision para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `COMPUTER_VISION_SUBSCRIPTION_KEY`a sua chave Computer Vision, chamada .

O seguinte código `main` adiciona um método à sua classe e cria variáveis para o ponto final e chave Azure do seu recurso. Terá de introduzir a sua própria corda de ponto final, que pode encontrar verificando a secção **de visão geral** do portal Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Em seguida, adicione o seguinte código para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) e passe-o para outro método(s), que definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

## <a name="analyze-an-image"></a>Analisar uma imagem

O seguinte código define `AnalyzeLocalImage`um método, que utiliza o objeto cliente para analisar uma imagem local e imprimir os resultados. O método devolve uma descrição de texto, categorização, lista de tags, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Em primeiro lugar, crie uma pasta **de recursos/pasta** no **src/main/pasta** do seu projeto e adicione uma imagem que gostaria de analisar. Em seguida, adicione a seguinte definição de método à sua aula **ComputerVisionQuickstarts.** Se necessário, altere `pathToLocalImage` o valor do ficheiro de imagem para corresponder. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Também pode analisar uma imagem remota utilizando o seu URL. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvam imagens remotas.

### <a name="specify-visual-features"></a>Especificar funcionalidades visuais

Em seguida, especifique quais as funcionalidades visuais que gostaria de extrair na sua análise. Consulte o [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) enum para obter uma lista completa.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analisar
Este método imprime resultados detalhados para a consola para cada âmbito de análise de imagem. Recomendamos que rodeie esta chamada de método num bloco Try/Catch. O método **analyzeImageInStream** devolve um objeto **ImageAnalysis** que contém toda a informação extraída.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

As seguintes secções mostram como analisar esta informação em detalhe.

### <a name="get-image-description"></a>Obtenha descrição da imagem

O código seguinte obtém a lista de legendas geradas para a imagem. Para mais informações, consulte [Descrever imagens](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O código seguinte obtém a categoria detetada da imagem. Para mais informações, consulte [imagens do Categorize](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O código seguinte obtém o conjunto de etiquetas detetadas na imagem. Para mais informações, consulte [as etiquetas de Conteúdo](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve os rostos detetados na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Para mais informações, consulte [a deteção do Rosto.](../../concept-detecting-faces.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código seguinte imprime a presença detetada de conteúdo adulto na imagem. Para mais informações, consulte [Adulto, conteúdo picante e sangrento.](../../concept-detecting-adult-content.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cor de imagem

O código que se segue imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do sotaque. Para mais informações, consulte [esquemas de cores](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico do domínio

A Computer Vision pode usar um modelo especializado para fazer uma análise mais aprofundada das imagens. Para mais informações, consulte [o conteúdo específico do Domínio](../../concept-detecting-domain-content.md). 

O código que se segue analisa dados sobre celebridades detetadas na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

O código que se segue analisa os dados sobre marcos detetados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código que se segue imprime informações sobre o tipo de imagem,&mdash;quer se trate de clip art ou de desenho de linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computorizada pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres.

> [!NOTE]
> Também pode ler texto numa imagem remota utilizando o seu URL. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvam imagens remotas.

### <a name="call-the-recognize-api"></a>Ligue para a API Recognise

Em primeiro lugar, utilize o seguinte código para chamar o método **textinStream reconhecido** para a imagem dada. Quando adicionar este código ao seu projeto, precisa `localTextImagePath` de substituir o valor do caminho para a sua imagem local. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Imprimir Reconhecer resultados

O seguinte bloco de código processa o texto devolvido e analisa-o para imprimir a primeira palavra em cada linha. Pode usar este código para entender rapidamente a estrutura de um caso **OcrResult.**

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Por fim, feche o bloco de tentativa/captura e a definição de método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar o pedido `gradle run` com o comando:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Computer Vision Java para fazer tarefas base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência da Visão Computacional (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [O que é a Imagem Digitalizada?](../../Home.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).