---
title: 'Quickstart: Biblioteca de clientes computer vision para Go'
titleSuffix: Azure Cognitive Services
description: Comece com a biblioteca de clientes computer vision para ir com este quickstart.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136015"
---
<a name="HOLTop"></a>

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Pacote](https://github.com/Azure/azure-sdk-for-go) de código[fonte](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | da biblioteca

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente de [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-computer-vision-azure-resource"></a>Criar um recurso Computer Vision Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para a Visão Computacional utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e, `COMPUTER_VISION_ENDPOINT`respectivamente.

### <a name="create-a-go-project-directory"></a>Criar um diretório de projeto Go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie `my-app`um novo espaço de trabalho para o seu projeto Go, nomeado, e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

O seu espaço de trabalho conterá três pastas:

* **sRC** - Este diretório conterá código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando irão neste diretório.
* **pkg** - Este diretório conterá os objetos de pacote Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório conterá os ficheiros binários `go install`executáveis que são criados quando executa .

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação linguística Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui `$GOPATH` informações para a definição e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instale a biblioteca de clientes para Go

Em seguida, instale a biblioteca de clientes para Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Criar uma aplicação Go

Em seguida, crie um ficheiro `sample-app.go`no diretório **src** chamado:

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` no seu IDE preferido ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Além disso, declare um contexto na raiz do seu guião. Você precisará deste objeto para executar a maioria das chamadas de função Computer Vision:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Em seguida, começará a adicionar código para realizar diferentes operações de Visão Computacional.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision Go SDK.

|Nome|Descrição|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Esta aula é necessária para todas as funcionalidades da Visão Computacional, tais como análise de imagem e leitura de texto. Você instantaneamente com a sua informação de subscrição, e você usa-o para fazer a maioria das operações de imagem.|
|[Análise de Imagens](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Este tipo contém os resultados de uma chamada de função **AnalyzeImage.** Existem tipos semelhantes para cada uma das funções específicas da categoria.|
|[Resultado da Operação de Leitura](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Este tipo contém os resultados de uma operação de leitura de lote. |
|[Tipos de Características Visuais](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Este tipo define os diferentes tipos de análise de imagem que podem ser feitos numa operação padrão da Análise. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes computer Vision para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este passo pressupõe que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` sua chave e ponto final da Visão Computacional, nomeado e respectivamente.

Crie `main` uma função e adicione-lhe o seguinte código para instantaneamente um cliente com o seu ponto final e chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código seguinte utiliza o objeto do cliente para analisar uma imagem remota e imprimir os resultados para a consola. Pode obter uma descrição de texto, categorização, lista de tags, objetos detetados, marcas detetadas, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Em primeiro lugar, guarde uma referência ao URL da imagem que pretende analisar. Coloque isto `main` dentro da sua função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Também pode analisar uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para cenários que envolvam imagens locais.

### <a name="specify-visual-features"></a>Especificar funcionalidades visuais

A função seguinte chama extrair diferentes características visuais da imagem da amostra. Definirá estas funções nas seguintes secções.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Obtenha descrição da imagem

A função seguinte obtém a lista de legendas geradas para a imagem. Para obter mais informações sobre a descrição da imagem, consulte [Descrever imagens](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

A função seguinte obtém a categoria detetada da imagem. Para mais informações, consulte [imagens do Categorize](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

A função seguinte obtém o conjunto de etiquetas detetadas na imagem. Para mais informações, consulte [as etiquetas de Conteúdo](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detetar objetos

A função seguinte deteta objetos comuns na imagem e imprime-os para a consola. Para mais informações, consulte a [deteção de objetos.](../../concept-object-detection.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detetar marcas

O código seguinte deteta marcas e logótipos corporativos na imagem e imprime-os para a consola. Para mais informações, [deteção da marca.](../../concept-brand-detection.md)

Primeiro, declare uma referência a `main` uma nova imagem dentro da sua função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

O código que se segue define a função de deteção da marca.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

A função seguinte devolve os rostos detetados na imagem com as suas coordenadas de retângulo e certos atributos faciais. Para mais informações, consulte [a deteção do Rosto.](../../concept-detecting-faces.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

A função seguinte imprime a presença detetada de conteúdo adulto na imagem. Para mais informações, consulte [Adulto, conteúdo picante e sangrento.](../../concept-detecting-adult-content.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cor de imagem

A função seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do sotaque. Para mais informações, consulte [esquemas de cores](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico do domínio

A Computer Vision pode utilizar modelos especializados para fazer uma análise mais aprofundada das imagens. Para mais informações, consulte [o conteúdo específico do Domínio](../../concept-detecting-domain-content.md). 

O código que se segue analisa dados sobre celebridades detetadas na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

O código que se segue analisa os dados sobre marcos detetados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

A função seguinte imprime informações sobre o tipo de imagem,&mdash;seja clip art ou um desenho de linha.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computorizada pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. O código nesta secção define `RecognizeTextReadAPIRemoteImage`uma função, que utiliza o objeto cliente para detetar e extrair texto impresso ou manuscrito na imagem.

Adicione a referência da imagem `main` da amostra e a chamada de função na sua função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Também pode extrair texto de uma imagem local. Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para cenários que envolvam imagens locais.

### <a name="call-the-read-api"></a>Ligue para a API read

Defina a nova função para ler texto, `RecognizeTextReadAPIRemoteImage`. Adicione o código abaixo, que chama o método **BatchReadFile** para a imagem dada. Este método devolve um ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Obter resultados de leitura

Em seguida, retire o ID de operação da chamada **BatchReadFile** e use-o com o método **GetReadOperationResult** para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Mostrar Resultados de ler

Adicione o seguinte código para analisar e exibir os dados de texto recuperados e termine a definição de função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação a `go run` partir do seu diretório de candidatura com o comando.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência a API da Visão Computacional (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [O que é a Imagem Digitalizada?](../../Home.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
