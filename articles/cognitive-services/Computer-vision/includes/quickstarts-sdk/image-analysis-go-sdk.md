---
title: 'Quickstart: Biblioteca de clientes de análise de imagem para Go'
titleSuffix: Azure Cognitive Services
description: Começa com a biblioteca de clientes image Analysis para Acompanhar este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073697"
---
<a name="HOLTop"></a>

Utilize a biblioteca do cliente de Análise de Imagem para analisar uma imagem para tags, descrição de texto, rostos, conteúdo adulto e muito mais.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Pacote](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A última versão de [Go](https://golang.org/dl/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-go-project-directory"></a>Criar um diretório de projeto go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go, nomeado `my-app` , e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

O seu espaço de trabalho conterá três pastas:

* **src** - Este diretório conterá código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando irão para este diretório.
* **pkg** - Este diretório conterá os objetos de embalagem Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório conterá os ficheiros binários executáveis que são criados quando executa `go install` .

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação da língua Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição `$GOPATH` e `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instale a biblioteca do cliente para Go

Em seguida, instale a biblioteca do cliente para Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Criar uma aplicação Go 

Em seguida, crie um ficheiro no **diretório src** chamado `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` no seu IDE preferido ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Além disso, declare um contexto na raiz do seu script. Você precisará deste objeto para executar a maioria das chamadas de função de Análise de Imagem:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Em seguida, começará a adicionar código para realizar diferentes operações de Visão Computacional.

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Image Analysis Go SDK.

|Nome|Descrição|
|---|---|
| [BaseCiente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador, como análise de imagem e leitura de texto. Você instantanea-o com as suas informações de subscrição, e você usá-lo para fazer a maioria das operações de imagem.|
|[ImagemAnalise](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Este tipo contém os resultados de uma chamada de função **AnalyzeImage.** Existem tipos semelhantes para cada uma das funções específicas da categoria.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Este tipo define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes de Análise de Imagem para Ir:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este passo pressupõe que [criou variáveis ambientais](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave de Visão de Computador e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

Crie uma `main` função e adicione-lhe o seguinte código para instantaneaizar um cliente com o seu ponto final e chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#analyze-an-image) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

O código que se segue utiliza o objeto do cliente para analisar uma imagem remota e imprimir os resultados para a consola. Você pode obter uma descrição de texto, categorização, lista de tags, objetos detetados, marcas detetadas, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Primeiro guarde uma referência ao URL da imagem que pretende analisar. Coloque isto dentro da sua `main` função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Também pode analisar uma imagem local. Consulte os métodos [baseClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) tais como **DescribeImageInStream**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para cenários que envolvam imagens locais.

### <a name="specify-visual-features"></a>Especifique as características visuais

As seguintes chamadas de função extraem diferentes características visuais da imagem da amostra. Definirá estas funções nas seguintes secções.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Obtenha a descrição da imagem

A seguinte função obtém a lista de legendas geradas para a imagem. Para obter mais informações sobre a descrição da imagem, consulte [descrever as imagens](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

A seguinte função obtém a categoria detetada da imagem. Para mais informações, consulte [as imagens categorize.](../../concept-categorizing-images.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

A seguinte função obtém o conjunto de tags detetadas na imagem. Para obter mais informações, consulte [as etiquetas de conteúdo.](../../concept-tagging-images.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detetar objetos

A seguinte função deteta objetos comuns na imagem e imprime-os na consola. Para obter mais informações, consulte [a deteção de objetos.](../../concept-object-detection.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detetar marcas

O código que se segue deteta marcas e logótipos corporativos na imagem e imprime-os na consola. Para mais informações, [deteção de marca.](../../concept-brand-detection.md)

Primeiro, declare uma referência a uma nova imagem dentro da sua `main` função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

O seguinte código define a função de deteção da marca.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

A função seguinte devolve os rostos detetados na imagem com as suas coordenadas de retângulo e certos atributos faciais. Para obter mais informações, consulte [a deteção do rosto.](../../concept-detecting-faces.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

A função seguinte imprime a presença detetada de conteúdo adulto na imagem. Para obter mais informações, consulte [Conteúdo Adulto, picante e sangrento.](../../concept-detecting-adult-content.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cores de imagem

A função seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do acento. Para obter mais informações, consulte [esquemas de cores.](../../concept-detecting-color-schemes.md)

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico de domínio

A Image Analysis pode usar modelos especializados para fazer mais análises nas imagens. Para obter mais informações, consulte [o conteúdo específico do Domínio.](../../concept-detecting-domain-content.md) 

O código seguinte analisa dados sobre celebridades detetadas na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

O código seguinte analisa dados sobre marcos detetados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenha o tipo de imagem

A função seguinte imprime informações sobre o tipo de &mdash; imagem, seja clip art ou um desenho de linha.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#run-the-application) [que encontrei num problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


## <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `go run` comando.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Corri o requerimento](?success=run-the-application#clean-up-resources) [que encontrei.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a instalar a biblioteca de clientes de Análise de Imagem e a fazer chamadas básicas de análise de imagem. Em seguida, saiba mais sobre as funcionalidades da API de análise.

> [!div class="nextstepaction"]
>[Ligue para a API de Análise](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Visão geral da análise de imagem](../../overview-image-analysis.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

