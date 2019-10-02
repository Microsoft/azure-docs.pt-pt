---
title: 'Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para .NET | Microsoft Docs'
description: Introdução à biblioteca de cliente do Pesquisa Visual Computacional para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: 27884d83b9ca828a81922d27fe958334665e664a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719525"
---
# <a name="quickstart-computer-vision-client-library-for-net"></a>Início rápido: Biblioteca de cliente Pesquisa Visual Computacional para .NET

Introdução à biblioteca de cliente do Pesquisa Visual Computacional para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Pesquisa Visual Computacional fornece acesso a algoritmos avançados para processamento de imagens e retorno de informações.

Use a biblioteca de cliente do Pesquisa Visual Computacional para .NET para:

* Analise uma imagem para marcas, descrição de texto, rostos, conteúdo adulto e muito mais.
* Reconheça o texto impresso e manuscrito com a API de leitura em lote.

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) |  | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) de pacote de[código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | da biblioteca de documentação de referência

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um Pesquisa Visual Computacional recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL `COMPUTER_VISION_SUBSCRIPTION_KEY` do ponto de extremidade, denominada e `COMPUTER_VISION_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Em uma janela de console (como cmd, PowerShell ou bash), use o `dotnet new` comando para criar um novo aplicativo de console com o nome. `computer-vision-quickstart` Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

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

No diretório do projeto, abra o arquivo *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes `using` diretivas:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Na classe **programa** do aplicativo, crie variáveis para o ponto de extremidade e a chave do Azure do recurso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

No diretório do aplicativo, instale a biblioteca de cliente do Pesquisa Visual Computacional para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK do .NET Pesquisa Visual Computacional.

|Name|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Essa classe é necessária para todas as funcionalidades de Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para fazer a maioria das operações de imagem.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Essa classe contém métodos adicionais para o **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitas em uma operação de análise padrão. Você especifica um conjunto de valores de VisualFeatureTypes dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Pesquisa Visual Computacional para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de `COMPUTER_VISION_SUBSCRIPTION_KEY`pesquisa Visual computacional, chamada.

Em um novo método, crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com sua chave e use-o com seu ponto de extremidade para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Você provavelmente desejará chamar esse método no `Main` método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código a seguir define um método `AnalyzeImageUrl`,, que usa o objeto de cliente para analisar uma imagem remota e imprimir os resultados. O método retorna uma descrição de texto, categorização, lista de marcas, faces detectadas, sinalizadores de conteúdo somente para adultos, cores principais e tipo de imagem.

Adicione a chamada de método em `Main` seu método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Configurar imagem de teste

Na classe **programa** , salve uma referência à URL da imagem que você deseja analisar.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Você também pode analisar uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) para ver cenários que envolvem imagens locais.

### <a name="specify-visual-features"></a>Especificar recursos visuais

Defina o novo método para análise de imagem. Adicione o código abaixo, que especifica os recursos visuais que você gostaria de extrair em sua análise. Consulte a enumeração [VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet) para obter uma lista completa.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Analisar

O método **AnalyzeImageAsync** retorna um objeto **ImageAnalysis** que contém todas as informações extraídas.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

As seções a seguir mostram como analisar essas informações detalhadamente.

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

Pesquisa Visual Computacional pode usar um modelo especializado para fazer mais análises sobre imagens. Consulte [conteúdo específico do domínio](../concept-detecting-domain-content.md) para obter mais detalhes. 

O código a seguir analisa dados sobre celebridades detectados na imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

O código a seguir analisa dados sobre os pontos de referência detectados na imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo&mdash;de imagem, seja Clip-Art ou desenho de linha.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

Pesquisa Visual Computacional pode ler o texto visível em uma imagem e convertê-lo em um fluxo de caracteres. O código nesta seção define um método, `ExtractTextUrl`, que usa o objeto de cliente para detectar e extrair texto impresso ou manuscrito na imagem.

Adicione a chamada de método em `Main` seu método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Configurar imagem de teste

Na classe **programa** , salve uma referência à URL da imagem da qual você deseja extrair o texto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Você também pode extrair texto de uma imagem local. Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) para ver cenários que envolvem imagens locais.

### <a name="call-the-read-api"></a>Chamar a API de leitura

Defina o novo método para ler o texto. Adicione o código abaixo, que chama o método **BatchReadFileAsync** para a imagem fornecida. Isso retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Obter resultados da leitura

Em seguida, obtenha a ID da operação retornada da chamada **BatchReadFileAsync** e use-a para consultar o serviço para obter os resultados da operação. O código a seguir verifica a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele imprime os dados de texto extraídos no console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Exibir resultados da leitura

Adicione o código a seguir para analisar e exibir os dados de texto recuperados e concluir a definição do método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no diretório do aplicativo com o `dotnet run` comando.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Referência de API da Pesquisa Visual Computacional (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [O que é o API da Pesquisa Visual Computacional?](../Home.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
