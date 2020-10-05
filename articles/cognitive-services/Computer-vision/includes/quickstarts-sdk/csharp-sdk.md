---
title: 'Quickstart: Biblioteca de clientes da Visão de Computador para .NET'
description: Neste arranque rápido, começa com a biblioteca de clientes da Computação Vision para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ec3e44c667d6821c4a6dc0779a760b65de5046e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321911"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão mais recente do [.NET Core SDK](https://dotnet.microsoft.com/download/).
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [Crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` e, respectivamente.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE. 

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `computer-vision-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *ComputerVisionQuickstart.cs*.

```dotnetcli
dotnet new console -n computer-vision-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```dotnetcli
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A partir do diretório do projeto, abra o ficheiro *ComputerVisionQuickstart.cs* no seu editor preferido ou IDE. Adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Na classe **programo** da aplicação, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Computer Vision para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0-preview.1
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision .NET SDK.

|Nome|Descrição|
|---|---|
| [ComputadorVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para fazer a maioria das operações de imagem.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Esta classe contém métodos adicionais para o **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de valores VisualFeatureTypes dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes da Visão de Computador para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart pressupõe que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a sua chave de Visão de Computador e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

Num novo método, instantaneamente um cliente com o seu ponto final e chave. Crie um objeto **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** com a sua chave e use-o com o seu ponto final para criar um objeto **[ComputerVisionClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

Provavelmente vai querer chamar este método no `Main` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código que se segue define um método, `AnalyzeImageUrl` que utiliza o objeto do cliente para analisar uma imagem remota e imprimir os resultados. O método devolve uma descrição de texto, categorização, lista de tags, rostos detetados, bandeiras de conteúdo adulto, cores principais e tipo de imagem.

Adicione o método de chamada no seu `Main` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Na sua aula **de Programa,** guarde uma referência ao URL da imagem que pretende analisar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

> [!NOTE]
> Também pode analisar uma imagem local. Consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) para ver cenários que envolvam imagens locais.

### <a name="specify-visual-features"></a>Especifique as características visuais

Defina o seu novo método de análise de imagem. Adicione o código abaixo, que especifica as funcionalidades visuais que gostaria de extrair na sua análise. Consulte os **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** enum para obter uma lista completa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

Insira qualquer um dos seguintes blocos de código no seu método **AnalyzeImageUrl** para implementar as suas funcionalidades. Lembre-se de adicionar um suporte de fecho no final.

```csharp
}
```

### <a name="analyze"></a>Análise

O método **AnalyzeImageAsync** devolve um objeto **ImageAnalysis** que contém toda a informação extraída.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

As seguintes secções mostram como analisar esta informação em detalhe.

### <a name="get-image-description"></a>Obtenha a descrição da imagem

O seguinte código obtém a lista de legendas geradas para a imagem. Consulte [as imagens](../../concept-describing-images.md) para mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O seguinte código obtém a categoria detetada da imagem. Consulte [as imagens categorize](../../concept-categorizing-images.md) para mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O seguinte código obtém o conjunto de tags detetadas na imagem. Consulte [as etiquetas de conteúdo](../../concept-tagging-images.md) para mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Detetar objetos

O código a seguir deteta objetos comuns na imagem e imprime-os na consola. Consulte [a deteção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Detetar marcas

O código que se segue deteta marcas e logótipos corporativos na imagem e imprime-os na consola. Consulte [a deteção da marca](../../concept-brand-detection.md) para mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve as faces detetadas na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Consulte [a deteção do rosto](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código que se segue imprime a presença detetada de conteúdo adulto na imagem. Consulte [o conteúdo adulto, picante e sangrento](../../concept-detecting-adult-content.md) para mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cores de imagem

O código seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do acento. Consulte [os esquemas de cores](../../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico de domínio

A Visão Computacional pode utilizar modelos especializados para fazer mais análises nas imagens. Consulte [o conteúdo específico do domínio](../../concept-detecting-domain-content.md) para obter mais detalhes. 

O código seguinte analisa dados sobre celebridades detetadas na imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

O código seguinte analisa dados sobre marcos detetados na imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenha o tipo de imagem

O código que se segue imprime informações sobre o tipo de &mdash; imagem, seja clip art ou um desenho de linha.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computacional pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Para obter mais informações sobre o reconhecimento de texto, consulte o doc conceptual de [reconhecimento de caracteres óticos (OCR).](../../concept-recognizing-text.md#read-api) O código nesta secção utiliza a mais recente [versão SDK da Visão De Computador para a Leitura 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) e define um método, `BatchReadFileUrl` que utiliza o objeto do cliente para detetar e extrair texto na imagem.

Adicione o método de chamada no seu `Main` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Configurar a imagem de teste

Na sua aula **de Programa,** guarde uma referência ao URL da imagem de que pretende extrair texto. Este snippet inclui imagens de amostra para texto impresso e manuscrito.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

> [!NOTE]
> Também pode extrair texto de uma imagem local. Consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) para ver cenários que envolvam imagens locais.

### <a name="call-the-read-api"></a>Ligue para a API de leitura

Defina o novo método de leitura do texto. Adicione o código abaixo, que chama o método **ReadAsync** para a imagem dada. Isto devolve um ID de operação e inicia um processo assíncrona para ler o conteúdo da imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_url)]

### <a name="get-read-results"></a>Obter resultados de Leitura

Em seguida, obtenha o ID de operação devolvido da chamada **ReadAsync,** e use-o para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_response)]

### <a name="display-read-results"></a>Mostrar Resultados de leitura

Adicione o seguinte código para analisar e exibir os dados de texto recuperados e terminar a definição do método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_read_display)]

## <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Referência API de Visão Computacional (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [O que é a Imagem Digitalizada?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
