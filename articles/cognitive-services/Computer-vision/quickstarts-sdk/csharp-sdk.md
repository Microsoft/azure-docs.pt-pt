---
title: 'Quickstart: Biblioteca de clientes Computer Vision para .NET'
description: Neste guia de início rápido, introdução à biblioteca de cliente Pesquisa Visual Computacional para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 61e7da2d82262b234d9352c7a1198d121d7f73f9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935771"
---
# <a name="quickstart-computer-vision-client-library-for-net"></a>Quickstart: Biblioteca de clientes Computer Vision para .NET

Inicie-se com a biblioteca de clientes computer vision para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. A Computer Vision fornece-lhe acesso a algoritmos avançados para processar imagens e devolver informações.

Utilize a biblioteca de clientes computer vision para .NET para:

* Analise uma imagem para tags, descrição de texto, rostos, conteúdo adulto e muito mais.
* Reconheça texto impresso e manuscrito com a API de leitura do lote.

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [do NuGet ( do pacote)de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [amostras biblioteca](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, denominado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando `dotnet new` para criar uma nova aplicação de consola com o nome `computer-vision-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes diretivas de `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Na aula de **Programa** da aplicação, crie variáveis para o ponto final e chave azure do seu recurso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Computer Vision para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision .NET SDK.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para fazer a maioria das operações de imagem.|
|[Extensões computerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Esta classe contém métodos adicionais para o **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Computer Vision para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a sua chave e ponto final da Visão computacional, nomeada `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

Em um novo método, crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto **[ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** com a sua chave e use-o com o seu ponto final para criar um objeto **[ComputerVisionClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Você provavelmente desejará chamar esse método no método `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código que se segue define um método, `AnalyzeImageUrl`, que utiliza o objeto do cliente para analisar uma imagem remota e imprimir os resultados. O método devolve uma descrição de texto, categorização, lista de tags, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

Adicione a chamada de método no seu método `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Na sua aula de **Programa,** guarde uma referência ao URL da imagem que pretende analisar.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Você também pode analisar uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) para ver cenários que envolvem imagens locais.

### <a name="specify-visual-features"></a>Especificar funcionalidades visuais

Defina o seu novo método de análise de imagem. Adicione o código abaixo, que especifica as funcionalidades visuais que gostaria de extrair na sua análise. Consulte o **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** enum para obter uma lista completa.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Analise

O método **AnalyzeImageAsync** devolve um objeto **ImageAnalysis** que contém toda a informação extraída.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

As seguintes secções mostram como analisar esta informação em detalhe.

### <a name="get-image-description"></a>Obter descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Consulte [descrever imagens](../concept-describing-images.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria de imagem

O código a seguir obtém a categoria detectada da imagem. Consulte [categorizar imagens](../concept-categorizing-images.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obter marcas de imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Consulte [marcas de conteúdo](../concept-tagging-images.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e os imprime no console do. Consulte [detecção de objeto](../concept-object-detection.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta marcas corporativas e logotipos na imagem e os imprime no console. Consulte [detecção de marca](../concept-brand-detection.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código a seguir retorna as faces detectadas na imagem com suas coordenadas de retângulo e seleciona atributos de face. Consulte [detecção facial](../concept-detecting-faces.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo adulto, erótico ou terríveis

O código a seguir imprime a presença detectada de conteúdo adulto na imagem. Consulte [conteúdo adulto, erótico, terríveis](../concept-detecting-adult-content.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Consulte [esquemas de cores](../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Computer Vision pode utilizar modelos especializados para fazer uma análise mais aprofundada das imagens. Consulte [conteúdo específico do domínio](../concept-detecting-domain-content.md) para obter mais detalhes. 

O código que se segue analisa dados sobre celebridades detetadas na imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código que se segue imprime informações sobre o tipo de imagem&mdash;se é clip art ou um desenho de linha.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres. O código nesta secção define um método, `ExtractTextUrl`, que utiliza o objeto cliente para detetar e extrair texto impresso ou manuscrito na imagem.

Adicione a chamada de método no seu método `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Na sua aula de **Programa,** guarde uma referência ao URL da imagem da sua deseja extrair texto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Também pode extrair texto de uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) para ver cenários que envolvem imagens locais.

### <a name="call-the-read-api"></a>Ligue para a API read

Defina o novo método de leitura do texto. Adicione o código abaixo, que chama o método **BatchReadFileAsync** para a imagem dada. Isto devolve um ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Obter resultados de leitura

Em seguida, retire o ID de operação da chamada **BatchReadFileAsync** e use-o para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Mostrar Resultados de ler

Adicione o seguinte código para analisar e exibir os dados de texto recuperados e termine a definição de método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no diretório do aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Referência API da Visão Computacional (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [O que é Pesquisa Visual Computacional?](../Home.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
