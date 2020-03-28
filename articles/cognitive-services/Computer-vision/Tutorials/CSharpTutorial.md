---
title: 'Amostra: Explore uma aplicação de processamento de imagem em C #'
titleSuffix: Azure Cognitive Services
description: Explore uma aplicação básica do Windows que utiliza a API computer Vision em Serviços Cognitivos Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b492d8e3bdcf6d9a41df3eb79ef159985cc715cf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168870"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Amostra: Explore uma aplicação de processamento de imagem com C #

Explore uma aplicação básica do Windows que utilize a Computer Vision para realizar o reconhecimento ótico de caracteres (OCR), crie miniaturas inteligentes, além de detetar, categorizar, etiquetar e descrever funcionalidades visuais, incluindo rostos, numa imagem. O exemplo abaixo permite submeter um URL de imagem ou um ficheiro armazenado localmente. Pode utilizar este exemplo de código aberto como modelo para a construção da sua própria aplicação para Windows utilizando a Computer Vision API e a Windows Presentation Foundation (WPF), uma parte da .NET Framework.

> [!div class="checklist"]
> * Obtenha a aplicação de amostra do GitHub
> * Abra e construa a app de amostras no Estúdio Visual
> * Execute a aplicação de amostra e interaja com ela para realizar vários cenários
> * Explore os vários cenários incluídos com a app de amostras

## <a name="prerequisites"></a>Pré-requisitos

Antes de explorar a aplicação de amostras, certifique-se de que cumpriu os seguintes pré-requisitos:

* Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
* Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave. Tome nota do URL final do serviço também.

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

A aplicação de amostra seletiva `Microsoft/Cognitive-Vision-Windows` Computer Vision está disponível no GitHub a partir do repositório. Este repositório também `Microsoft/Cognitive-Common-Windows` inclui o repositório como um submódulo Git. Pode clonar recursivamente este repositório, incluindo o submódulo, quer utilizando o `git clone --recurse-submodules` comando a partir da linha de comando, quer utilizando o GitHub Desktop.

Por exemplo, para clonar recursivamente o repositório da aplicação de amostra de Visão Computacional a partir de um pedido de comando, executar o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Não descarregue este repositório como UM ZIP. Git não inclui submódulos ao descarregar um repositório como um ZIP.

### <a name="get-optional-sample-images"></a>Obtenha imagens de amostra opcionais

Pode utilizar opcionalmente as imagens [Face](../../Face/Overview.md) da amostra incluídas na aplicação `Microsoft/Cognitive-Face-Windows` face sample, disponível no GitHub a partir do repositório. Essa aplicação de `/Data`amostra inclui uma pasta, que contém várias imagens de pessoas. Também pode clonar este repositório de forma recorrente pelos métodos descritos para a aplicação de amostra sinuosa Computer Vision.

Por exemplo, para clonar recursivamente o repositório da aplicação face sample a partir de um pedido de comando, executar o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Abra e construa a app de amostras no Estúdio Visual

Você deve construir a aplicação de amostra primeiro, para que o Visual Studio possa resolver dependências, antes de poder executar ou explorar a aplicação de amostras. Para abrir e construir a aplicação de amostras, faça os seguintes passos:

1. Abra o ficheiro de `/Sample-WPF/VisionAPI-WPF-Samples.sln`solução Visual Studio, no Estúdio Visual.
1. Certifique-se de que a solução Visual Studio contém dois projetos:  

   * Biblioteca de Controlo de Utilizadores de Amostras
   * VisionAPI-WPF-Samples  

   Se o projeto SampleUserControlLibrary não estiver disponível, confirme que `Microsoft/Cognitive-Vision-Windows` clonou recursivamente o repositório.
1. No Estúdio Visual, ou prima Ctrl+Shift+B ou escolha **Construir** a partir do menu da fita e, em seguida, escolha **Build Solution** para construir a solução.

## <a name="run-and-interact-with-the-sample-app"></a>Executar e interagir com a app de amostras

Pode executar a aplicação de amostras, para ver como interage consigo e com a biblioteca de clientes computer Vision ao executar várias tarefas, tais como gerar miniaturas ou marcar imagens. Para executar e interagir com a aplicação de amostra, faça os seguintes passos:

1. Depois da construção estar completa, prima **F5** ou escolha **Debug** no menu da fita e, em seguida, escolha **iniciar a depuração** para executar a aplicação de amostra.
1. Quando a aplicação da amostra for apresentada, escolha a **Gestão** da Chave de Assinatura do painel de navegação para exibir a página de Gestão da Chave de Assinatura.
   ![Página de Gestão de Chaves de Assinatura](../Images/Vision_UI_Subscription.PNG)  
1. Introduza a sua chave de subscrição na **Chave de Subscrição**.
1. Introduza o URL do ponto final no **Ponto Final**.  
   Por exemplo, se estiver a utilizar a chave de subscrição do teste gratuito computer Vision, introduza o url final seguinte:`https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Se não quiser introduzir a sua chave de subscrição e url de ponto final da próxima vez que executar a aplicação de amostra, escolha **'Save Setting'** para guardar a chave de subscrição e o URL final do ponto final para o computador. Se pretender eliminar a chave de subscrição previamente guardada e o URL de ponto final, escolha **eliminar a definição**.

   > [!NOTE]
   > A aplicação de amostra `System.IO.IsolatedStorage`utiliza armazenamento isolado e, para armazenar a chave de subscrição e URL final.

1. Em **selecione um cenário** no painel de navegação, selecione um dos cenários atualmente incluídos com a aplicação de amostra:  

   | Cenário | Descrição |
   |----------|-------------|
   |Analisar imagem | Utiliza a operação [Análise imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para analisar uma imagem local ou remota. Pode escolher as características visuais e a linguagem para a análise, e ver tanto a imagem como os resultados.  |
   |Analisar imagem com modelo de domínio | Utiliza a operação ['Modelos Específicos de Domínio listada'](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) para listar os modelos de domínio a partir dos quais pode selecionar, e a operação [Recognise Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) para analisar uma imagem local ou remota utilizando o modelo de domínio selecionado. Pode também escolher o idioma para a análise. |
   |Descrever imagem | Utiliza a operação [Descrever](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) imagem para criar uma descrição legível pelo homem de uma imagem local ou remota. Também pode escolher o idioma para a descrição. |
   |Gerar Tags | Utiliza a operação [Tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) para marcar as características visuais de uma imagem local ou remota. Pode também escolher o idioma utilizado para as etiquetas. |
   |Reconhecer texto (OCR) | Utiliza a operação [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para reconhecer e extrair texto impresso a partir de uma imagem. Pode escolher o idioma para usar ou deixar a Visão Computacional detetar automaticamente o idioma. |
   |Reconhecer texto V2 (inglês) | Utiliza as operações de resultado de [reconheço](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) texto e [reconhecimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) de texto para reconhecer e extrair sincronicamente texto impresso ou manuscrito a partir de uma imagem. |
   |Obter Miniatura | Utiliza a operação [Get Miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) para gerar uma miniatura para uma imagem local ou remota. |

   A imagem seguinte ilustra a página fornecida para o cenário de Imagem de Análise, depois de analisar uma imagem de amostra.
   ![Screenshot da página de imagem de Analisar](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Explore a aplicação de amostras

A solução Visual Studio para a aplicação de amostra seleção computer vision contém dois projetos:

* Biblioteca de Controlo de Utilizadores de Amostras  
  O projeto SampleUserControlLibrary fornece uma funcionalidade partilhada por várias amostras de Serviços Cognitivos. O projeto contém o seguinte:
  * Cenários de amostra  
    Um UserControl que fornece uma apresentação padronizada, como a barra de título, painel de navegação e painel de conteúdo, para amostras. A aplicação de amostra seleção computer vision utiliza este controlo na janela MainWindow.xaml para exibir páginas de cenários e aceder a informações partilhadas entre cenários, tais como a chave de subscrição e o URL do ponto final.
  * Página de assinaturas  
    Uma página que fornece um layout padronizado para introduzir uma chave de subscrição e URL de ponto final para a aplicação de amostra. A aplicação de amostra seleção computer vision utiliza esta página para gerir a chave de subscrição e o URL de ponto final utilizado pelas páginas do cenário.
  * Controlo de Resultados de Vídeo  
    Um UserControl que fornece uma apresentação padronizada para informações de vídeo. A aplicação de amostra seleção computer vision não utiliza este controlo.
* VisionAPI-WPF-Samples  
  O principal projeto para a aplicação de amostras Computer Vision, este projeto contém toda a funcionalidade interessante para a Visão computacional. O projeto contém o seguinte:
  * AnalyzeInDomainPage.xaml  
    A página de cenário para a Imagem de Análise com cenário de Modelo de Domínio.
  * AnalyzeImage.xaml  
    A página de cenário para o cenário de Análise imagem.
  * DescreverPage.xaml  
    A página do cenário para o cenário de Descrever imagem.
  * ImageScenarioPage.cs  
    A classe ImageScenarioPage, a partir da qual são derivadas todas as páginas de cenário na aplicação da amostra. Esta classe gere a funcionalidade, como fornecer credenciais e formatação, partilhada por todas as páginas do cenário.
  * MainWindow.xaml  
    A janela principal da aplicação de amostra, utiliza o controlo SampleScenarios para apresentar as páginas De Sição ChavePage e cenário.
  * OCRPage.xaml  
    A página de cenário para o cenário Reconhecer Texto (OCR).
  * RecognizeLanguage.cs  
    A classe RecogniseLanguage, que fornece informações sobre os idiomas suportados pelos vários métodos na aplicação da amostra.
  * TagsPage.xaml  
    A página de cenário para o cenário De Gerar Tags.
  * Página de Reconhecimento de Texto.xaml  
    A página de cenário para o cenário Recognise Text V2 (Inglês).
  * MiniaturaPage.xaml  
    A página de cenário para o cenário Get Miniatura.

### <a name="explore-the-sample-code"></a>Explore o código da amostra

As principais porções do código da amostra `KEY SAMPLE CODE STARTS HERE` são `KEY SAMPLE CODE ENDS HERE`emolduradas com blocos de comentários que começam e terminam com, para facilitar a exploração da aplicação da amostra. Estas principais porções do código da amostra contêm o código mais relevante para aprender a usar a biblioteca de clientes DaPI computer Vision para fazer várias tarefas. Pode procurar `KEY SAMPLE CODE STARTS HERE` no Estúdio Visual para se mover entre as secções de código mais relevantes na aplicação de amostra seleção computer vision. 

Por exemplo, `UploadAndAnalyzeImageAsync` o método, mostrado a seguir e incluído no AnalyzePage.xaml, demonstra como usar a `ComputerVisionClient.AnalyzeImageInStreamAsync` biblioteca do cliente para analisar uma imagem local invocando o método.

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

### <a name="explore-the-client-library"></a>Explore a biblioteca de clientes

Esta aplicação de amostra utiliza a biblioteca de clientes Computer Vision API, um invólucro de cliente C# fino para a API computer Vision em Serviços Cognitivos Azure. A biblioteca de clientes está disponível a partir do NuGet no pacote [Microsoft.Azure.CognitiveServices.Vision.ComputerVision.](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) Quando construiu a aplicação Visual Studio, recuperou a biblioteca do cliente do seu pacote NuGet correspondente. Também pode ver o código fonte para `/ClientLibrary` a `Microsoft/Cognitive-Vision-Windows` biblioteca do cliente na pasta do repositório.

A funcionalidade da biblioteca de `ComputerVisionClient` clientes centra-se em torno da classe, no espaço de `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` nome, enquanto os modelos utilizados pela `ComputerVisionClient` classe ao interagir com a Computer Vision são encontrados no espaço de `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` nome. Nas várias páginas de cenário XAML incluídas na aplicação de amostras, encontrará as seguintes `using` diretivas para esses espaços de nome:

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

Você vai saber mais sobre os vários métodos incluídos com a `ComputerVisionClient` classe enquanto você explora os cenários incluídos com a app de amostras Computer Vision.

## <a name="explore-the-analyze-image-scenario"></a>Explore o cenário de Imagem de Análise

Este cenário é gerido pela página AnalyzePage.xaml. Pode escolher as características visuais e a linguagem para a análise, e ver tanto a imagem como os resultados. A página de cenário fá-lo utilizando um dos seguintes métodos, dependendo da origem da imagem:

* UploadAndAnalyzeImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.AnalyzeImageInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* AnalyzeUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.AnalyzeImageAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndAnalyzeImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a, em seguida, obtém as características visuais e a linguagem selecionadas na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, `Stream` passando o para o ficheiro, as características visuais `ImageAnalysis` e a linguagem, e depois devolve o resultado como exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `AnalyzeUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Obtém as características visuais e a linguagem selecionadas na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, passando o URL de imagem, as características `ImageAnalysis` visuais e a linguagem, e depois devolve o resultado como um exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Explore a Imagem de Análise com o cenário do Modelo de Domínio

Este cenário é gerido pela página AnalyzeInDomainPage.xaml. Pode escolher um modelo de `celebrities` `landmarks`domínio, como ou, e linguagem para realizar uma análise específica do domínio da imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* GetAvailableDomainModelsAsync  
  Este método obtém a lista de modelos `_domainModelComboBox` de domínio disponíveis a partir `ComputerVisionClient.ListModelsAsync` da Computer Vision e povoa o controlo ComboBox na página, utilizando o método.
* UploadAndAnalyzeInDomainImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* AnalyzeInDomainUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.AnalyzeImageByDomainAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndAnalyzeInDomainImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a, em seguida, obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` método, `Stream` passando o para o ficheiro, o nome do modelo de `DomainModelResults` domínio, e a linguagem, e depois devolve o resultado como uma instância. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `AnalyzeInDomainUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Obtém a linguagem selecionada na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageByDomainAsync` método, passando o URL de imagem, as características `DomainModelResults` visuais e a linguagem, e depois devolve o resultado como um exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-describe-image-scenario"></a>Explore o cenário de descrever imagem

Este cenário é gerido pela página DescreverPage.xaml. Pode escolher uma linguagem para criar uma descrição legível pelo homem da imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndDescreverImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.DescribeImageInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* DescreverUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.DescribeImageAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndDescribeImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a, em seguida, obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.DescribeImageInStreamAsync` método, `Stream` passando o para o ficheiro, o número máximo de candidatos (neste caso, 3), e a língua, e depois devolve o resultado como exemplo. `ImageDescription` Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `DescribeUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Obtém a linguagem selecionada na página do cenário. Chama o `ComputerVisionClient.DescribeImageAsync` método, passando o URL de imagem, o número máximo de candidatos (neste `ImageDescription` caso, 3), e a linguagem, e depois devolve o resultado como exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-generate-tags-scenario"></a>Explore o cenário de Geração de Tags

Este cenário é gerido pela página TagsPage.xaml. Pode escolher um idioma para marcar as características visuais de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndGetTagsForImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.TagImageInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* GenerateTagsForUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.TagImageAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndGetTagsForImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a, em seguida, obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.TagImageInStreamAsync` método, `Stream` passando o para o ficheiro e `TagResult` a linguagem, e depois devolve o resultado como um caso. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `GenerateTagsForUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Obtém a linguagem selecionada na página do cenário. Chama o `ComputerVisionClient.TagImageAsync` método, passando o URL de imagem e `TagResult` a linguagem, e depois devolve o resultado como um caso. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Explore o cenário de Texto Reconhecido (OCR)

Este cenário é gerido pela página OCRPage.xaml. Pode escolher uma língua para reconhecer e extrair texto impresso a partir de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecogniseImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.RecognizePrintedTextInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* ReconhecerUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.RecognizePrintedTextAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndRecognizeImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a, em seguida, obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.RecognizePrintedTextInStreamAsync` método, indicando que a orientação não é detetada e passa ndo o `Stream` para o ficheiro e a língua, e depois devolve o resultado como exemplo. `OcrResult` Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Obtém a linguagem selecionada na página do cenário. Chama o `ComputerVisionClient.RecognizePrintedTextAsync` método, indicando que a orientação não é detetada e passa `OcrResult` ndo o URL de imagem e a linguagem, em seguida, devolve o resultado como um caso. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Explore o cenário reconhecer o texto V2 (inglês)

Este cenário é gerido pela página TextRecognitionPage.xaml. Pode escolher o modo de reconhecimento e uma linguagem para reconhecer e extrair sincronicamente texto impresso ou manuscrito de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecogniseImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `RecognizeAsync` ser codificada como a e `ComputerVisionClient.RecognizeTextInStreamAsync` enviada para a Visão Computacional, chamando o método e passando um delegado parametrizado para o método.
* ReconhecerUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `RecognizeAsync` URL para a imagem é `ComputerVisionClient.RecognizeTextAsync` enviado para a Visão Computacional, chamando o método e passando um delegado parametrizado para o método.
* ReconhecerAsync Este método lida com o apelo assíncrono tanto para os métodos como `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` para os métodos, bem como para a sondagem para resultados, chamando o `ComputerVisionClient.GetTextOperationResultAsync` método.

Ao contrário dos outros cenários incluídos na aplicação de amostra sincronia, este cenário é assíncrono, na forma como um método é chamado para iniciar o processo, mas um método diferente é chamado para verificar o estado e devolver os resultados desse processo. O fluxo lógico neste cenário é um pouco diferente daquele nos outros cenários.

O `UploadAndRecognizeImageAsync` método abre o ficheiro `imageFilePath` local especificado `Stream`para leitura `RecognizeAsync` como a, em seguida, chama o método, passando:

* Uma expressão de lambda para um delegado assíncrono paramétrico do `ComputerVisionClient.RecognizeTextInStreamAsync` método, com o `Stream` para `GetHeadersAsyncFunc`o ficheiro e o modo de reconhecimento como parâmetros, em .
* Uma expressão de lambda para `Operation-Location` um delegado `GetOperationUrlFunc`obter o valor do cabeçalho de resposta, em .

O `RecognizeUrlAsync` método `RecognizeAsync` chama o método, passando:

* Uma expressão de lambda para um delegado assíncrono paramétrico do `ComputerVisionClient.RecognizeTextAsync` método, com o URL da `GetHeadersAsyncFunc`imagem remota e o modo de reconhecimento como parâmetros, em .
* Uma expressão de lambda para `Operation-Location` um delegado `GetOperationUrlFunc`obter o valor do cabeçalho de resposta, em .

Quando `RecognizeAsync` o método é `UploadAndRecognizeImageAsync` concluído, ambos e `RecognizeUrlAsync` `TextOperationResult` métodos devolvem o resultado como um exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeAsync` método chama o delegado parametrizado para o `ComputerVisionClient.RecognizeTextInStreamAsync` método ou `ComputerVisionClient.RecognizeTextAsync` o método passado `GetHeadersAsyncFunc` e aguarda a resposta. O método chama então `GetOperationUrlFunc` o `Operation-Location` delegado passado para obter o valor do cabeçalho de resposta da resposta. Este valor é o URL usado para recuperar `GetHeadersAsyncFunc` os resultados do método transmitido pela Computer Vision.

O `RecognizeAsync` método chama `ComputerVisionClient.GetTextOperationResultAsync` então o método, `Operation-Location` passando o URL recuperado do cabeçalho `GetHeadersAsyncFunc`de resposta, para obter o estado e o resultado do método passado em . Se o estado não indicar que o método foi concluído, `RecognizeAsync` com `ComputerVisionClient.GetTextOperationResultAsync` sucesso ou sem sucesso, o método chama mais 3 vezes, esperando 3 segundos entre chamadas. O `RecognizeAsync` método devolve os resultados ao método que lhe chamou.

## <a name="explore-the-get-thumbnail-scenario"></a>Explore o cenário Get Miniatura

Este cenário é gerido pela página MiniaturaPage.xaml. Pode indicar se deve usar a colheita inteligente e especificar a altura e a largura desejadas, para gerar uma miniatura a partir de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndMiniaturaImageAsync  
  Este método é utilizado para imagens locais, nas `Stream` quais a imagem deve `ComputerVisionClient.GenerateThumbnailInStreamAsync` ser codificada como a e enviada para a Visão Computacional, chamando o método.
* MiniaturaUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o `ComputerVisionClient.GenerateThumbnailAsync` URL para a imagem é enviado para a Visão Computacional, chamando o método.

O `UploadAndThumbnailImageAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Como a aplicação de amostraestá a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão Computacional. Abre o ficheiro local especificado `imageFilePath` para `Stream`leitura como a . Chama o `ComputerVisionClient.GenerateThumbnailInStreamAsync` método, passando a `Stream` largura, a altura, o para o ficheiro, e `Stream`se deve usar a colheita inteligente, e depois devolve o resultado como a . Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeUrlAsync` método cria `ComputerVisionClient` uma nova instância, utilizando a chave de subscrição especificada e o URL do ponto final. Chama o `ComputerVisionClient.GenerateThumbnailAsync` método, passando a largura, a altura, o URL para a imagem, e `Stream`se usar a colheita inteligente, em seguida, devolve o resultado como a . Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine `Microsoft/Cognitive-Vision-Windows` a pasta na qual clonou o repositório. Se optou por utilizar as imagens da amostra, também `Microsoft/Cognitive-Face-Windows` elimine a pasta na qual clonou o repositório.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o serviço Face](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
