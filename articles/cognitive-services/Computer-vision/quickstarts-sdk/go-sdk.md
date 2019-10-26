---
title: 'Início rápido: biblioteca de cliente do Pesquisa Visual Computacional para o go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do Pesquisa Visual Computacional para o go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: pafarley
ms.openlocfilehash: f05ef80d34d1c0635bdcdca244aacba6185565fb
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958414"
---
# <a name="quickstart-computer-vision-client-library-for-go"></a>Início rápido: biblioteca de cliente do Pesquisa Visual Computacional para o go

Introdução à biblioteca de cliente do Pesquisa Visual Computacional para o go. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Pesquisa Visual Computacional fornece acesso a algoritmos avançados para processamento de imagens e retorno de informações.

Use a biblioteca de cliente Pesquisa Visual Computacional para ir para:

* Analise uma imagem para marcas, descrição de texto, rostos, conteúdo adulto e muito mais.
* Reconheça o texto impresso e manuscrito com a API de leitura em lote.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [pacote](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente do [go](https://golang.org/dl/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL do ponto de extremidade, denominada `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

### <a name="create-a-go-project-directory"></a>Criar um diretório do projeto Go

Em uma janela de console (cmd, PowerShell, terminal, bash), crie um novo espaço de trabalho para seu projeto Go, chamado `my-app`e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Seu espaço de trabalho conterá três pastas:

* **src** -esse diretório conterá o código-fonte e os pacotes. Todos os pacotes instalados com o comando `go get` entrarão nesse diretório.
* **pkg** -esse diretório conterá os objetos compilados do pacote go. Todos esses arquivos têm uma extensão `.a`.
* **compartimento** -esse diretório conterá os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação do idioma go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para definir `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de cliente para o go

Em seguida, instale a biblioteca de cliente para o Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

ou, se você usar o Dep, na execução do seu repositório:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Criar um aplicativo go

Em seguida, crie um arquivo no diretório **src** chamado `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` em seu IDE ou editor de texto preferencial. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Além disso, declare um contexto na raiz do seu script. Você precisará desse objeto para executar a maioria Pesquisa Visual Computacional chamadas de função:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Em seguida, você começará a adicionar o código para executar diferentes operações de Pesquisa Visual Computacional.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK do Pesquisa Visual Computacional go.

|Nome|Descrição|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional, como análise de imagem e leitura de texto. Você a instancia com suas informações de assinatura e a usa para fazer a maioria das operações de imagem.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Esse tipo contém os resultados de uma chamada de função **AnalyzeImage** . Há tipos semelhantes para cada uma das funções específicas da categoria.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Esse tipo contém os resultados de uma operação de leitura em lote. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Esse tipo define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Você especifica um conjunto de valores de VisualFeatureTypes dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a Pesquisa Visual Computacional biblioteca de cliente para o Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Esta etapa pressupõe que você [criou variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de pesquisa Visual computacional e ponto de extremidade, chamados `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

Crie uma função `main` e adicione o código a seguir a ela para criar uma instância de um cliente com o ponto de extremidade e a chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código a seguir usa o objeto de cliente para analisar uma imagem remota e imprimir os resultados no console. Você pode obter uma descrição de texto, categorização, lista de marcas, objetos detectados, marcas detectadas, faces detectadas, sinalizadores de conteúdo somente para adultos, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Primeiro, salve uma referência à URL da imagem que você deseja analisar. Coloque isso dentro de sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Você também pode analisar uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para ver cenários que envolvem imagens locais.

### <a name="specify-visual-features"></a>Especificar recursos visuais

As chamadas de função a seguir extraem recursos visuais diferentes da imagem de exemplo. Você definirá essas funções nas seções a seguir.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Obter descrição da imagem

A função a seguir obtém a lista de legendas geradas para a imagem. Para obter mais informações sobre a descrição da imagem, consulte [descrever imagens](../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Obter categoria de imagem

A função a seguir obtém a categoria detectada da imagem. Para obter mais informações, consulte [categorizar imagens](../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Obter marcas de imagem

A função a seguir obtém o conjunto de marcas detectadas na imagem. Para obter mais informações, consulte [marcas de conteúdo](../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Detectar objetos

A função a seguir detecta objetos comuns na imagem e os imprime no console do. Para obter mais informações, consulte [detecção de objetos](../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta marcas corporativas e logotipos na imagem e os imprime no console. Para obter mais informações, [detecção de marca](../concept-brand-detection.md).

Primeiro, declare uma referência a uma nova imagem dentro de sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

O código a seguir define a função de detecção de marca.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

A função a seguir retorna as faces detectadas na imagem com suas coordenadas de retângulo e determinados atributos de face. Para obter mais informações, consulte [detecção facial](../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo adulto, erótico ou terríveis

A função a seguir imprime a presença detectada de conteúdo adulto na imagem. Para obter mais informações, consulte [conteúdo adulto, erótico, terríveis](../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

A função a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Para obter mais informações, consulte [esquemas de cores](../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

Pesquisa Visual Computacional pode usar modelos especializados para fazer mais análises sobre imagens. Para obter mais informações, consulte [conteúdo específico do domínio](../concept-detecting-domain-content.md). 

O código a seguir analisa dados sobre celebridades detectados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

A função a seguir imprime informações sobre o tipo de imagem&mdash;se é Clip-Art ou um desenho de linha.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres. O código nesta seção define uma função, `RecognizeTextReadAPIRemoteImage`, que usa o objeto de cliente para detectar e extrair texto impresso ou manuscrito na imagem.

Adicione a referência de imagem de exemplo e a chamada de função em sua função `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Você também pode extrair texto de uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para ver cenários que envolvem imagens locais.

### <a name="call-the-read-api"></a>Chamar a API de leitura

Defina a nova função para ler o texto, `RecognizeTextReadAPIRemoteImage`. Adicione o código abaixo, que chama o método **BatchReadFile** para a imagem fornecida. Esse método retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Obter resultados da leitura

Em seguida, obtenha a ID da operação retornada da chamada **BatchReadFile** e use-a com o método **GetReadOperationResult** para consultar o serviço em busca de resultados da operação. O código a seguir verifica a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele imprime os dados de texto extraídos no console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Exibir resultados da leitura

Adicione o código a seguir para analisar e exibir os dados de texto recuperados e concluir a definição da função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no diretório do aplicativo com o comando `go run`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de API da Pesquisa Visual Computacional (GO)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [O que é o API da Pesquisa Visual Computacional?](../Home.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
