---
title: 'Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para Java'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do Pesquisa Visual Computacional para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 6d40eb9bf3b90fb66002b964aca0db42b76094bb
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261985"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para Java

Introdução à biblioteca de cliente do Pesquisa Visual Computacional para Java. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Pesquisa Visual Computacional fornece acesso a algoritmos avançados para processamento de imagens e retorno de informações.

Use a biblioteca de cliente do Pesquisa Visual Computacional para Java para:

* Analise uma imagem para marcas, descrição de texto, rostos, conteúdo adulto e muito mais.
* Reconheça o texto impresso e manuscrito com a API de leitura em lote.

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [do artefato da documentação de referência (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de compilação gradle](https://gradle.org/install/)ou outro gerenciador de dependência.

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a cadeia de `COMPUTER_VISION_SUBSCRIPTION_KEY` caracteres do ponto de extremidade de serviço, denominada e `COMPUTER_VISION_ENDPOINT`, respectivamente.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto gradle

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `gradle init` comando do seu diretório de trabalho. Este comando criará arquivos de Build essenciais para gradle, incluindo *Build. gradle. KTs*, que é usado em tempo de execução para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando for solicitado a escolher uma **DSL**, selecione **Kotlin**.

Localize *Build. gradle. KTs* e abra-o com seu IDE ou editor de texto preferencial. Em seguida, copie a seguinte configuração de compilação. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ComputerVisionQuickstarts**. Ele importa a biblioteca de Pesquisa Visual Computacional.

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

Em seu diretório de trabalho, execute o seguinte comando para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *ComputerVisionQuickstarts. java*. Abra-o em seu editor ou IDE preferido e adicione as `import` seguintes instruções:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Em seguida, adicione uma definição de classe para **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Este guia de início rápido usa o gradle Dependency Manager. Você pode encontrar a biblioteca de cliente e informações para outros gerenciadores de dependência no [repositório central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

No arquivo *Build. gradle. KTs* do seu projeto, inclua a biblioteca de cliente do pesquisa Visual computacional como uma dependência.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK do Java Pesquisa Visual Computacional.

|Name|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Essa classe vem do objeto de cliente e lida diretamente com todas as operações de imagem, como análise de imagem, detecção de texto e geração de miniaturas.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Você especifica um conjunto de valores de VisualFeatureTypes dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Visual Computacional para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de `COMPUTER_VISION_SUBSCRIPTION_KEY`pesquisa Visual computacional, chamada.

O código a seguir adiciona `main` um método à sua classe e cria variáveis para o ponto de extremidade e a chave do Azure do recurso. Você precisará inserir sua própria cadeia de caracteres de ponto de extremidade, que pode ser encontrada verificando a seção **visão geral** do portal do Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Em seguida, adicione o código a seguir para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) e passá-lo para outros métodos, que você definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Se você criou a variável de ambiente depois de iniciar o aplicativo, precisará fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

## <a name="analyze-an-image"></a>Analisar uma imagem

O código a seguir define um método `AnalyzeLocalImage`,, que usa o objeto de cliente para analisar uma imagem local e imprimir os resultados. O método retorna uma descrição de texto, categorização, lista de marcas, faces detectadas, sinalizadores de conteúdo somente para adultos, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Primeiro, crie um **recurso/** pasta na pasta **src/main/** do seu projeto e adicione uma imagem que você gostaria de analisar. Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts** . Se necessário, altere o valor de `pathToLocalImage` para corresponder ao arquivo de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Você também pode analisar uma imagem remota usando sua URL. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvem imagens remotas.

### <a name="specify-visual-features"></a>Especificar recursos visuais

Em seguida, especifique quais recursos visuais você gostaria de extrair em sua análise. Consulte a enumeração [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) para obter uma lista completa.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analisar
Esse método imprime resultados detalhados no console para cada escopo de análise de imagem. Recomendamos que você envolva essa chamada de método em um bloco try/catch. O método **analyzeImageInStream** retorna um objeto **ImageAnalysis** que contém todas as informações extraídas.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

As seções a seguir mostram como analisar essas informações detalhadamente.

### <a name="get-image-description"></a>Obter descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Consulte [descrever imagens](../concept-describing-images.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Obter categoria de imagem

O código a seguir obtém a categoria detectada da imagem. Consulte [categorizar imagens](../concept-categorizing-images.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Obter marcas de imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Consulte [marcas de conteúdo](../concept-tagging-images.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Detetar rostos

O código a seguir retorna as faces detectadas na imagem com suas coordenadas de retângulo e seleciona atributos de face. Consulte [detecção facial](../concept-detecting-faces.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-or-racy-content"></a>Detectar conteúdo adulto ou erótico

O código a seguir imprime a presença detectada de conteúdo adulto ou erótico na imagem. Consulte [conteúdo adulto e erótico](../concept-detecting-adult-content.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Consulte [esquemas de cores](../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

Pesquisa Visual Computacional pode usar um modelo especializado para fazer mais análises sobre imagens. Consulte [conteúdo específico do domínio](../concept-detecting-domain-content.md) para obter mais detalhes. 

O código a seguir analisa dados sobre celebridades detectados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo&mdash;de imagem, seja Clip-Art ou desenho de linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres.

> [!NOTE]
> Você também pode ler texto em uma imagem remota usando sua URL. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvem imagens remotas.

### <a name="call-the-recognize-api"></a>Chamar a API de reconhecimento

Primeiro, use o código a seguir para chamar o método **recognizePrintedTextInStream** para a imagem fornecida. Ao adicionar esse código ao seu projeto, você precisa substituir o valor de `localTextImagePath` pelo caminho para a imagem local. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

### <a name="print-recognize-results"></a>Imprimir resultados de reconhecimento

O bloco de código a seguir processa o texto retornado e o analisa para imprimir a primeira palavra em cada linha. Você pode usar esse código para entender rapidamente a estrutura de uma instância do **OcrResult** .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Por fim, feche o bloco try/catch e a definição do método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Executar a aplicação

Você pode compilar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o `gradle run` comando:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Pesquisa Visual Computacional biblioteca Java para realizar tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência de Pesquisa Visual Computacional (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [O que é Pesquisa Visual Computacional?](../Home.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).