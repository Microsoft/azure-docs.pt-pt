---
title: 'Exemplo: Explorar uma aplicação de processamento de imagem noC#'
titleSuffix: Azure Cognitive Services
description: Explore uma aplicação básica do Windows que utiliza a API de imagem digitalizada nos serviços cognitivos do Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 63b5130e3cade54a2fbc432b2391ad3ee1ea8a1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60408190"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Exemplo: Explorar uma aplicação de processamento de imagem comC#

Explore uma aplicação básica do Windows que utiliza o de imagem digitalizada para realizar o reconhecimento ótico de carateres (OCR), criar miniaturas de smart-recortada, além de detectar, categorizar, etiqueta e descreve recursos visual, incluindo as faces, numa imagem. O exemplo abaixo permite submeter um URL de imagem ou um ficheiro armazenado localmente. Pode utilizar este exemplo de código-fonte aberto como um modelo para criar sua própria aplicação para o Windows usando a API de imagem digitalizada e o Windows Presentation Foundation (WPF), uma parte do .NET Framework.

> [!div class="checklist"]
> * Obter a aplicação de exemplo do GitHub
> * Abrir e criar a aplicação de exemplo no Visual Studio
> * Executar a aplicação de exemplo e interagir com ele para executar vários cenários
> * Explore os vários cenários incluídos com a aplicação de exemplo

## <a name="prerequisites"></a>Pré-requisitos

Antes de explorar a aplicação de exemplo, certifique-se de que cumpriu os pré-requisitos seguintes:

* Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
* Tem de ter uma chave de subscrição da Imagem Digitalizada. Pode obter uma chave de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a imagem digitalizada e obtenha a chave.

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

A aplicação de exemplo de imagem digitalizada está disponível no GitHub a partir do `Microsoft/Cognitive-Vision-Windows` repositório. Este repositório também inclui o `Microsoft/Cognitive-Common-Windows` repositório como um submodule de Git. Pode clonar recursivamente este repositório, incluindo submodule, através da utilização do `git clone --recurse-submodules` comando da linha de comandos ou utilizando o ambiente de trabalho do GitHub.

Por exemplo, para recursivamente clonar o repositório para a aplicação de exemplo de visão do computador a partir de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Não transferir este repositório como um ZIP. Git não inclui submódulos durante o download de um repositório como um ZIP.

### <a name="get-optional-sample-images"></a>Obter imagens de exemplo opcional

Opcionalmente, pode utilizar imagens de exemplo incluídas com o [Face](../../Face/Overview.md) aplicação de exemplo disponível no GitHub a partir do `Microsoft/Cognitive-Face-Windows` repositório. Esse aplicativo de exemplo inclui uma pasta, `/Data`, que contém várias imagens de pessoas. Pode clonar recursivamente este repositório, também, através dos métodos descritos para a aplicação de exemplo de imagem digitalizada.

Por exemplo, para recursivamente clonar o repositório para a aplicação de exemplo Face a partir de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Abrir e criar a aplicação de exemplo no Visual Studio

Deve criar a aplicação de exemplo em primeiro lugar, para que o Visual Studio pode resolver dependências, antes de poder executar ou explorar a aplicação de exemplo. Para abrir e criar a aplicação de exemplo, efetue os seguintes passos:

1. Abra o ficheiro de solução do Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`, no Visual Studio.
1. Certifique-se de que a solução do Visual Studio contém dois projetos:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Se o projeto de SampleUserControlLibrary não estiver disponível, certifique-se que já recursivamente clonou a `Microsoft/Cognitive-Vision-Windows` repositório.
1. No Visual Studio, prima Ctrl + Shift + B ou escolha **crie** no menu da faixa de opções e, em seguida, escolha **compilar solução** para compilar a solução.

## <a name="run-and-interact-with-the-sample-app"></a>Executar e interagir com a aplicação de exemplo

Pode executar a aplicação de exemplo, para ver como ele interage com e com a biblioteca de cliente de imagem digitalizada quando efetuar várias tarefas, como gerar miniaturas ou imagens de etiquetagem. Para executar e interagir com a aplicação de exemplo, siga os passos abaixo:

1. Após a compilação estiver concluída, ou prima **F5** ou escolha **depurar** no menu da faixa de opções e, em seguida, escolha **iniciar a depuração** para executar a aplicação de exemplo.
1. Quando a aplicação de exemplo é apresentada, escolha **gestão de chaves de subscrição** no painel de navegação para exibir a página de gestão de chaves de subscrição.
   ![Página de gerenciamento de chave de subscrição](../Images/Vision_UI_Subscription.PNG)  
1. Introduza a chave de subscrição **chave de subscrição**.
1. Introduza o URL de ponto de extremidade, omitindo os `/vision/v1.0`, do recurso de imagem digitalizada para a chave de subscrição **ponto final**.  
   Por exemplo, se estiver a utilizar a chave de subscrição de avaliação gratuita de imagem digitalizada, introduza o seguinte URL de ponto final para a oeste Central-na região do Azure: `https://westcentralus.api.cognitive.microsoft.com`
1. Se não pretender introduzir a sua chave de subscrição e o ponto final escolha da próxima vez que executar a aplicação de exemplo, de URL **Guardar definição** para guardar o URL de ponto final e a chave de subscrição para o seu computador. Se pretender eliminar o seu URL de ponto final e a chave de subscrição tinha guardado, escolha **eliminar definição**.

   > [!NOTE]
   > A aplicação de exemplo utiliza o armazenamento isolado, e `System.IO.IsolatedStorage`, para armazenar o URL de ponto final e a chave de subscrição.

1. Sob **selecionar um cenário** no painel de navegação, selecione um dos cenários atualmente incluídos com a aplicação de exemplo:  

   | Cenário | Descrição |
   |----------|-------------|
   |Analisar a imagem | Utiliza a [analisar imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) operação para analisar uma imagem local ou remota. Pode escolher os recursos visual e a linguagem para a análise e ver a imagem e os resultados.  |
   |Analisar a imagem com o modelo de domínio | Utiliza a [lista, modelos de domínios específicos](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) operação para listar os modelos de domínio a partir da qual pode selecionar, e o [reconhecer domínio específico conteúdo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) operação para analisar uma imagem de local ou remota com o modelo de domínio selecionado. Também pode escolher o idioma para a análise. |
   |Descrever a imagem | Utiliza a [descrevem imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) operação para criar uma descrição legível por humanos de uma imagem local ou remota. Também pode escolher o idioma para a descrição. |
   |Gerar etiquetas | Utiliza a [Tag de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) operação etiquetar os recursos visual de uma imagem local ou remoto. Também pode escolher o idioma utilizado para as etiquetas. |
   |Reconhecer texto (OCR) | Utiliza a [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) operação para reconhecer e extrair impresso texto a partir de uma imagem. Pode escolher o idioma a utilizar ou deixar de imagem digitalizada detetar automaticamente o idioma. |
   |Reconhecer texto V2 (em inglês) | Utiliza a [reconhecer texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) e [obter Resultado da operação texto reconhecer](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) operações para reconhecer e extrair texto impresso ou manuscrito a partir de uma imagem de forma assíncrona. |
   |Obter miniatura | Utiliza a [obter miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) operação para gerar uma miniatura para ver uma imagem local ou remota. |

   Captura de ecrã seguinte ilustra a página fornecida para o cenário de imagem de analisar, depois de analisar uma imagem de exemplo.
   ![Captura de ecrã da página de imagem analisar](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Explorar a aplicação de exemplo

A solução do Visual Studio para a aplicação de exemplo de imagem digitalizada contém dois projetos:

* SampleUserControlLibrary  
  O projeto de SampleUserControlLibrary fornece uma funcionalidade partilhada por vários exemplos de serviços cognitivos. O projeto contém o seguinte:
  * SampleScenarios  
    Um UserControl que fornece uma apresentação padronizada, tais como a barra de título, o painel de navegação e o painel de conteúdo, exemplos de mensagens em fila. A aplicação de exemplo de imagem digitalizada utiliza esse controle na janela de mainwindow. XAML para exibir páginas de cenário e aceder às informações partilhadas entre cenários, tais como o URL de ponto final e a chave de subscrição.
  * SubscriptionKeyPage  
    Uma página que fornece um esquema padronizado para introduzir um URL de ponto final e a chave de subscrição para a aplicação de exemplo. A aplicação de exemplo de imagem digitalizada utiliza esta página para gerir a chave de subscrição e o URL de ponto final utilizado pelas páginas de cenário.
  * VideoResultControl  
    Um UserControl que fornece uma apresentação padronizada para informações do vídeo. A aplicação de exemplo de imagem digitalizada não usa esse controle.
* VisionAPI-WPF-Samples  
  O projeto principal para a aplicação de exemplo de imagem digitalizada, esse projeto contém toda a funcionalidade interessante a para imagem digitalizada. O projeto contém o seguinte:
  * AnalyzeInDomainPage.xaml  
    A página de cenário para a imagem de analisar com o cenário de modelo de domínio.
  * AnalyzeImage.xaml  
    A página de cenário para o cenário de imagem de analisar.
  * DescribePage.xaml  
    A página de cenário para o cenário de imagem de descrever.
  * ImageScenarioPage.cs  
    A classe ImageScenarioPage, partir da qual derivam todas as páginas de cenário no aplicativo de exemplo. Essa classe gerencia as funcionalidades, como credenciais e formatar as saídas, partilhada por todas as páginas de cenário.
  * MainWindow.xaml  
    A janela principal para a aplicação de exemplo, ele usa o controle de SampleScenarios para apresentar as páginas de cenário e SubscriptionKeyPage.
  * OCRPage.xaml  
    A página de cenário para o cenário de reconhecer texto (OCR).
  * RecognizeLanguage.cs  
    A classe RecognizeLanguage, que fornece informações sobre os idiomas suportados pelos diversos métodos no aplicativo de exemplo.
  * TagsPage.xaml  
    A página de cenário para o cenário de gerar etiquetas.
  * TextRecognitionPage.xaml  
    A página de cenário para o cenário de (em inglês) de reconhecer V2 de texto.
  * ThumbnailPage.xaml  
    A página de cenário para o cenário de obter miniatura.

### <a name="explore-the-sample-code"></a>Explore o código de exemplo

Principais partes desse código de exemplo são formuladas com blocos de comentário que começam com `KEY SAMPLE CODE STARTS HERE` e terminar com `KEY SAMPLE CODE ENDS HERE`, para que seja mais fácil para que possa explorar a aplicação de exemplo. Estas principais partes desse código de exemplo contêm o código mais relevante para aprender a utilizar a biblioteca de cliente de API de imagem digitalizada fazer várias tarefas. Pode procurar `KEY SAMPLE CODE STARTS HERE` no Visual Studio para se movimentar entre as secções mais relevantes de código no aplicativo de exemplo de imagem digitalizada. 

Por exemplo, o `UploadAndAnalyzeImageAsync` método, mostrado a seguir e incluído no AnalyzePage.xaml, demonstra como utilizar a biblioteca de cliente para analisar uma imagem local invocando o `ComputerVisionClient.AnalyzeImageInStreamAsync` método.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Explore a biblioteca de cliente

Esta aplicação de exemplo utiliza a biblioteca de cliente de API de imagem digitalizada, fina c# wrapper do cliente para a API de imagem digitalizada nos serviços cognitivos do Azure. A biblioteca de clientes está disponível a partir do NuGet na [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) pacote. Quando criou o aplicativo do Visual Studio, que obteve a biblioteca de cliente do seu pacote NuGet correspondente. Também pode ver o código-fonte para a biblioteca de cliente no `/ClientLibrary` pasta do `Microsoft/Cognitive-Vision-Windows` repositório.

Funcionalidade a biblioteca de cliente centra-se no `ComputerVisionClient` , além de classe a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` espaço de nomes, enquanto os modelos usados pela `ComputerVisionClient` encontram-se na classe ao interagir com a visão do computador a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` espaço de nomes. As páginas de cenário XAML vários incluídas com a aplicação de exemplo, encontrará o seguinte `using` diretivas para esses espaços de nomes:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Irá aprender mais sobre os diversos métodos que acompanha o `ComputerVisionClient` de classe à medida que explora os cenários incluídos com a aplicação de exemplo de imagem digitalizada.

## <a name="explore-the-analyze-image-scenario"></a>Explorar o cenário de analisar imagem

Este cenário é gerido pela página AnalyzePage.xaml. Pode escolher os recursos visual e a linguagem para a análise e ver a imagem e os resultados. A página de cenário faz isso através de um dos seguintes métodos, dependendo da origem da imagem:

* UploadAndAnalyzeImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.AnalyzeImageInStreamAsync` método.
* AnalyzeUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.AnalyzeImageAsync` método.

O `UploadAndAnalyzeImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`, em seguida, obtém os recursos visual e o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, passando o `Stream` para o ficheiro, os recursos visual e o idioma, em seguida, devolve o resultado como um `ImageAnalysis` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `AnalyzeUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Obtém os recursos visual e o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, passando o URL da imagem, os recursos visual e a linguagem, em seguida, devolve o resultado como um `ImageAnalysis` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Explorar a imagem de analisar com o cenário de modelo de domínio

Este cenário é gerido pela página AnalyzeInDomainPage.xaml. Pode escolher um modelo de domínio, tal como `celebrities` ou `landmarks`e o idioma para executar uma análise específica do domínio da imagem e veja a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* GetAvailableDomainModelsAsync  
  Este método obtém a lista de modelos de domínio disponíveis a partir de imagem digitalizada e preenche o `_domainModelComboBox` controle de caixa de combinação na página, usando o `ComputerVisionClient.ListModelsAsync` método.
* UploadAndAnalyzeInDomainImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` método.
* AnalyzeInDomainUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.AnalyzeImageByDomainAsync` método.

O `UploadAndAnalyzeInDomainImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`, em seguida, obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` método, passando o `Stream` para o ficheiro, o nome do modelo de domínio e o idioma, em seguida, devolve o resultado como um `DomainModelResults` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `AnalyzeInDomainUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.AnalyzeImageByDomainAsync` método, passando o URL da imagem, os recursos visual e a linguagem, em seguida, devolve o resultado como um `DomainModelResults` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="explore-the-describe-image-scenario"></a>Explorar o cenário de descrever imagem

Este cenário é gerido pela página DescribePage.xaml. Pode escolher um idioma para criar uma descrição legível por humanos da imagem e ver a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndDescribeImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.DescribeImageInStreamAsync` método.
* DescribeUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.DescribeImageAsync` método.

O `UploadAndDescribeImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`, em seguida, obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.DescribeImageInStreamAsync` método, passando o `Stream` para o ficheiro, o número máximo de candidatos (no caso, 3) e o idioma, em seguida, devolve o resultado como um `ImageDescription` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `DescribeUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.DescribeImageAsync` método, passando o URL da imagem, o número máximo de candidatos (no caso, 3) e o idioma, em seguida, devolve o resultado como um `ImageDescription` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="explore-the-generate-tags-scenario"></a>Explorar o cenário de gerar etiquetas

Este cenário é gerido pela página TagsPage.xaml. Pode escolher um idioma para marcar os recursos visual de uma imagem e ver a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndGetTagsForImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.TagImageInStreamAsync` método.
* GenerateTagsForUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.TagImageAsync` método.

O `UploadAndGetTagsForImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`, em seguida, obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.TagImageInStreamAsync` método, passando o `Stream` para o ficheiro e o idioma, em seguida, devolve o resultado como um `TagResult` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `GenerateTagsForUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.TagImageAsync` método, passando o URL da imagem e o idioma, em seguida, devolve o resultado como um `TagResult` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Explorar o cenário de reconhecer texto (OCR)

Este cenário é gerido pela página OCRPage.xaml. Pode escolher um idioma para reconhecer e extrair texto impresso a partir de uma imagem e ver a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.RecognizePrintedTextInStreamAsync` método.
* RecognizeUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.RecognizePrintedTextAsync` método.

O `UploadAndRecognizeImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`, em seguida, obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.RecognizePrintedTextInStreamAsync` método, que indica que a orientação não é detetada e passando o `Stream` para o ficheiro e o idioma, em seguida, devolve o resultado como um `OcrResult` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `RecognizeUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Obtém o idioma selecionado na página de cenário. Ele chama o `ComputerVisionClient.RecognizePrintedTextAsync` método, que indica que a orientação não é detetada e passando o URL da imagem e o idioma, em seguida, devolve o resultado como um `OcrResult` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Explorar o cenário de (em inglês) de reconhecer V2 de texto

Este cenário é gerido pela página TextRecognitionPage.xaml. Pode escolher o modo de reconhecimento e um idioma para reconhecer e extrair texto impresso ou manuscrito a partir de uma imagem de forma assíncrona e ver a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `RecognizeAsync` método e passando um delegado parametrizado o `ComputerVisionClient.RecognizeTextInStreamAsync` método.
* RecognizeUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada ao chamar o `RecognizeAsync` método e passando um delegado parametrizado o `ComputerVisionClient.RecognizeTextAsync` método.
* RecognizeAsync esse método processa a chamada assíncrona para ambos os `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` métodos, bem como a consulta para obter os resultados ao chamar o `ComputerVisionClient.GetTextOperationResultAsync` método.

Ao contrário de outros cenários incluídos na aplicação de exemplo de imagem digitalizada, este cenário é assíncrono, em que um método é chamado para iniciar o processo, mas um outro método é chamado para verificar o estado e devolver os resultados do processo. O fluxo lógico neste cenário é um pouco diferente do que nos outros cenários.

O `UploadAndRecognizeImageAsync` método abre o arquivo local especificado na `imageFilePath` para leitura como uma `Stream`, em seguida, chama o `RecognizeAsync` método, passando:

* Uma expressão lambda para um delegado assíncrona parametrizado dos `ComputerVisionClient.RecognizeTextInStreamAsync` método, com o `Stream` para o ficheiro e o modo de reconhecimento como parâmetros, no `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado obter o `Operation-Location` valor de cabeçalho de resposta, em `GetOperationUrlFunc`.

O `RecognizeUrlAsync` chamadas de método o `RecognizeAsync` método, passando:

* Uma expressão lambda para um delegado assíncrona parametrizado dos `ComputerVisionClient.RecognizeTextAsync` método, com o URL da imagem remota e o modo de reconhecimento como parâmetros, no `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado obter o `Operation-Location` valor de cabeçalho de resposta, em `GetOperationUrlFunc`.

Quando o `RecognizeAsync` método é concluído, ambos `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` métodos retornam o resultado como um `TextOperationResult` instância. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `RecognizeAsync` método chama o delegado parametrizado por qualquer o `ComputerVisionClient.RecognizeTextInStreamAsync` ou `ComputerVisionClient.RecognizeTextAsync` método passado `GetHeadersAsyncFunc` e aguarda pela resposta. O método, em seguida, chama o delegado transmitido `GetOperationUrlFunc` para obter o `Operation-Location` valor de cabeçalho de resposta da resposta. Este valor é o URL utilizado para obter os resultados do método passado `GetHeadersAsyncFunc` de imagem digitalizada.

O `RecognizeAsync` , em seguida, chama o método a `ComputerVisionClient.GetTextOperationResultAsync` método, passando o URL obtido a partir do `Operation-Location` cabeçalho de resposta, para obter o estado e o resultado do método passado `GetHeadersAsyncFunc`. Se o estado não indica que o método concluída, com ou sem êxito, o `RecognizeAsync` chamadas de método `ComputerVisionClient.GetTextOperationResultAsync` 3 mais vezes, a aguardar 3 segundos entre as chamadas. O `RecognizeAsync` método retorna os resultados para o método que o chamou.

## <a name="explore-the-get-thumbnail-scenario"></a>Explorar o cenário de obter miniatura

Este cenário é gerido pela página ThumbnailPage.xaml. Pode indicar se pretende utilizar o corte inteligente e especifique a altura desejada e a largura, para gerar uma miniatura de uma imagem e ver a imagem e os resultados. A página de cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndThumbnailImageAsync  
  Este método é utilizado para imagens locais, na qual a imagem tem de ser codificada como um `Stream` e enviado para imagem digitalizada chamando o `ComputerVisionClient.GenerateThumbnailInStreamAsync` método.
* ThumbnailUrlAsync  
  Este método é utilizado para imagens remotas, no qual o URL para a imagem é enviado para imagem digitalizada chamando o `ComputerVisionClient.GenerateThumbnailAsync` método.

O `UploadAndThumbnailImageAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Uma vez que a aplicação de exemplo é analisar uma imagem do local, tem de enviar o conteúdo da imagem para imagem digitalizada. Ele abre o ficheiro local especificado no `imageFilePath` para leitura como uma `Stream`. Ele chama o `ComputerVisionClient.GenerateThumbnailInStreamAsync` método, passando a largura, altura, o `Stream` para o ficheiro e se deve utilizar o corte inteligente, em seguida, devolve o resultado como um `Stream`. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

O `RecognizeUrlAsync` método cria uma nova `ComputerVisionClient` de instância, utilizando o URL de ponto final e a chave de subscrição especificada. Ele chama o `ComputerVisionClient.GenerateThumbnailAsync` método, passando a largura, altura, o URL para a imagem e se deve utilizar o corte inteligente, em seguida, devolve o resultado como um `Stream`. Os métodos herdados do `ImageScenarioPage` classe apresentar resultados devolvidos na página de cenário.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine a pasta onde clonou o `Microsoft/Cognitive-Vision-Windows` repositório. Se tiver optado por utilizar imagens de exemplo, também elimina a pasta onde clonou o `Microsoft/Cognitive-Face-Windows` repositório.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução à API de rostos](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
