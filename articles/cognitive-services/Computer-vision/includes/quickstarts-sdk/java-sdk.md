---
title: 'Quickstart: Biblioteca de clientes de reconhecimento de caracteres óticos para Java'
description: Neste quickstart, começa com a biblioteca de clientes de reconhecimento de caracteres óticos para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: c1322eac7fd5ec1ead09a7b0197db931f96f8d48
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073486"
---
<a name="HOLTop"></a>

Utilize a biblioteca de clientes de reconhecimento de caracteres óticos para ler texto impresso e manuscrito em imagens.

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

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)que contém os exemplos de código neste arranque rápido.

Navegue para a nova pasta e crie um ficheiro chamado *ComputerVisionQuickstarts.java*. Abra-o no seu editor preferido ou IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontre a chave de subscrição e o ponto final

Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a chave de subscrição e o ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 

Defina a classe **ComputerVisionQuickstarts**. Crie variáveis para a sua chave de subscrição de Visão de Computador e ponto final. Cole a chave de subscrição e o ponto final no seguinte código, quando indicado. O seu ponto final de visão de computador tem o formulário `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de subscrição do seu código quando terminar e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vai definir isto mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_readinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]
> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do OCR Java SDK.

|Nome|Descrição|
|---|---|
| [ComputadorVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes.|

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes OCR para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instantânea um objeto [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) com o seu ponto final e chave.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço OCR pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Esta secção define um método, `ReadFromFile` que toma um caminho de arquivo local e imprime o texto da imagem para a consola.

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

Neste arranque rápido, aprendeu a instalar a biblioteca de clientes OCR e a utilizar a API de Leitura. Em seguida, saiba mais sobre as funcionalidades da API de Leitura.

> [!div class="nextstepaction"]
>[Ligue para a API de leitura](../../Vision-API-How-to-Topics/call-read-api.md)

* [Visão geral do OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
