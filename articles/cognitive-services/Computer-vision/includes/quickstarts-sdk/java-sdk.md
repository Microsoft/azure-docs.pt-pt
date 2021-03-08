---
title: 'Quickstart: Biblioteca de clientes da Visão de Computador para Java'
description: Neste arranque rápido, começa com a biblioteca de clientes da Visão De Computador para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 125b5b7353ef5ea038221cb241012ca23e3645aa
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445202"
---
<a name="HOLTop"></a>

Utilize a biblioteca do cliente da Visão De Computador para:

* Analise uma imagem para tags, descrição de texto, rostos, conteúdo adulto, e muito mais.
* Leia texto impresso e manuscrito com a API de Leitura.

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/computervision)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | [Artefacto (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
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

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Pode encontrar a biblioteca do cliente e informações para outros gestores de dependência no [Repositório Central de Maven.](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)

Localize *build.gradle.kts* e abra-o com o seu IDE ou editor de texto preferido. Em seguida, copie na seguinte configuração de construção. Esta configuração define o projeto como uma aplicação Java cujo ponto de entrada é a classe **ComputerVisionQuickstarts**. Importa a biblioteca de Visão De Computador.

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
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Criar um ficheiro Java

A partir do seu diretório de trabalho, executar o seguinte comando para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *ComputerVisionQuickstarts.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)que contém os exemplos de código neste arranque rápido.

Defina a classe **ComputerVisionQuickstarts**.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_1)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_2)]

Dentro da classe **ComputerVisionQuickstarts,** crie variáveis para a chave e ponto final do seu recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vai definir isto mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision Java SDK.

|Nome|Descrição|
|---|---|
| [ComputadorVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| Esta classe provém do objeto do cliente e lida diretamente com todas as operações de imagem, tais como análise de imagem, deteção de texto e geração de miniaturas.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes da Visão de Computador para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instantânea um objeto [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) com o seu ponto final e chave.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#analyze-an-image) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

O seguinte código define um método, `AnalyzeLocalImage` que usa o objeto do cliente para analisar uma imagem local e imprimir os resultados. O método devolve uma descrição de texto, categorização, lista de tags, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

> [!TIP]
> Também pode analisar uma imagem remota utilizando o seu URL. Consulte os métodos [ComputerVision,](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) tais como **AnalyzeImage**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvam imagens remotas.

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Em primeiro lugar, crie um **recursos/pasta** na pasta **src/main/do** seu projeto e adicione uma imagem que gostaria de analisar. Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts.** Altere o valor do `pathToLocalImage` para corresponder ao seu ficheiro de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Especifique as características visuais

Em seguida, especifique quais as funcionalidades visuais que gostaria de extrair na sua análise. Consulte os [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes) enum para obter uma lista completa.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analisar
Este bloco imprime resultados detalhados na consola para cada âmbito de análise de imagem. O método **analyzeImageInStream** devolve um objeto **ImageAnalysis** que contém toda a informação extraída.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

As seguintes secções mostram como analisar esta informação em detalhe.

### <a name="get-image-description"></a>Obtenha a descrição da imagem

O seguinte código obtém a lista de legendas geradas para a imagem. Para mais informações, consulte [descrever imagens.](../../concept-describing-images.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O seguinte código obtém a categoria detetada da imagem. Para mais informações, consulte [as imagens categorize.](../../concept-categorizing-images.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O seguinte código obtém o conjunto de tags detetadas na imagem. Para obter mais informações, consulte [as etiquetas de conteúdo.](../../concept-tagging-images.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve as faces detetadas na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Para obter mais informações, consulte [a deteção do rosto.](../../concept-detecting-faces.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-objects"></a>Detetar objetos

O seguinte código devolve os objetos detetados na imagem com as suas coordenadas. Para obter mais informações, consulte [a deteção de objetos.](../../concept-object-detection.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_objects)]


### <a name="detect-brands"></a>Detetar marcas

O código seguinte devolve os logótipos da marca detetados na imagem com as suas coordenadas. Para mais informações, consulte [a deteção da marca.](../../concept-brand-detection.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_brands)]



### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código que se segue imprime a presença detetada de conteúdo adulto na imagem. Para obter mais informações, consulte [Conteúdo Adulto, picante e sangrento.](../../concept-detecting-adult-content.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cores de imagem

O código seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do acento. Para obter mais informações, consulte [esquemas de cores.](../../concept-detecting-color-schemes.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico de domínio

A Visão Computacional pode usar um modelo especializado para fazer mais análises nas imagens. Para obter mais informações, consulte [o conteúdo específico do Domínio.](../../concept-detecting-domain-content.md) 

O código seguinte analisa dados sobre celebridades detetadas na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

O código seguinte analisa dados sobre marcos detetados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Obtenha o tipo de imagem

O código que se segue imprime informações sobre o tipo de &mdash; imagem, quer se trate de clip art ou desenho de linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#read-printed-and-handwritten-text) [que encontrei num problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image)

### <a name="close-out-the-method"></a>Feche o método

Complete o bloco de tentativa/captura e feche o método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyze_catch)]


## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computacional pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Esta secção define um método, `ReadFromFile` que toma um caminho de arquivo local e imprime o texto da imagem para a consola.

> [!TIP]
> Também pode ler texto numa imagem remota referenciada por URL. Consulte os métodos [ComputerVision,](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) tais como **ler**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para cenários que envolvam imagens remotas.

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Crie um **recurso/pasta** na pasta **src/main/do** seu projeto e adicione uma imagem a partir da qual gostaria de ler texto. Você pode baixar uma [imagem de amostra](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) para usar aqui.

Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts.** Altere o valor do `localFilePath` para corresponder ao seu ficheiro de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Ligue para a API de leitura

Em seguida, adicione o seguinte código para ligar para o método **readInStreamWithServiceResponseAsync** para a imagem dada.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

O seguinte bloco de código extrai o ID de operação da resposta da chamada De leitura. Utiliza este ID com um método de ajuda para imprimir os resultados da leitura de texto na consola. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Feche o bloco de tentativa/captura e a definição de método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Obter resultados de Leitura

Em seguida, adicione uma definição para o método do ajudante. Este método utiliza o ID de operação do passo anterior para consultar a operação de leitura e obter os resultados do OCR quando estão disponíveis.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

O resto do método analisa os resultados do OCR e imprime-os na consola.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Por fim, adicione o outro método de ajuda utilizado acima, que extrai o ID de operação da resposta inicial.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Li um sms](?success=read-printed-handwritten-text#run-the-application) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar a aplicação com o `gradle run` comando:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Corri o requerimento](?success=run-the-application#clean-up-resources) [que encontrei.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Computer Vision Java para fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência da Visão Computacional (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [O que é a Imagem Digitalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
