---
title: 'Amostra: Explore uma aplicação de processamento de imagem em C #'
titleSuffix: Azure Cognitive Services
description: Explore uma aplicação básica do Windows que utiliza a API de Visão De Computador em Serviços Cognitivos Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 522cf801e7c1bfdd3ed1f452c123a2db701c0c42
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008808"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Amostra: Explore uma aplicação de processamento de imagem com C #

Explore uma aplicação básica do Windows que usa a Visão de Computador para realizar o reconhecimento de caracteres óticos (OCR), crie miniaturas de corte inteligente, além de detetar, categorizar, marcar e descrever funcionalidades visuais, incluindo rostos, numa imagem. O exemplo abaixo permite submeter um URL de imagem ou um ficheiro armazenado localmente. Pode utilizar este exemplo de código aberto como modelo para construir a sua própria aplicação para Windows utilizando a API de Visão De Computador e a Fundação de Apresentação do Windows (WPF), uma parte do Quadro .NET.

> [!div class="checklist"]
> * Obtenha o aplicativo de amostra do GitHub
> * Abra e construa a app de amostras no Visual Studio
> * Execute a app da amostra e interaja com ela para realizar vários cenários
> * Explore os vários cenários incluídos na app da amostra

## <a name="prerequisites"></a>Pré-requisitos

Antes de explorar a aplicação da amostra, certifique-se de que encontrou os seguintes pré-requisitos:

* Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

A aplicação de amostra de Visão de Computador está disponível no GitHub a partir do `Microsoft/Cognitive-Vision-Windows` repositório. Este repositório também inclui o `Microsoft/Cognitive-Common-Windows` repositório como uma submódula git. Pode clonar novamente este repositório, incluindo o submodule, quer utilizando o `git clone --recurse-submodules` comando da linha de comando, quer utilizando o Ambiente de Trabalho do GitHub.

Por exemplo, para clonar novamente o repositório para a aplicação de amostra de Visão de Computador a partir de um pedido de comando, executar o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Não descarregue este repositório como ZIP. Git não inclui submodulos ao descarregar um repositório como ZIP.

### <a name="get-optional-sample-images"></a>Obtenha imagens de amostra opcionais

Pode utilizar opcionalmente as imagens da amostra incluídas na aplicação [face](../../Face/Overview.md) sample, disponível no GitHub a partir do `Microsoft/Cognitive-Face-Windows` repositório. A aplicação da amostra inclui uma pasta, `/Data` que contém múltiplas imagens de pessoas. Pode clonar novamente este repositório, também, pelos métodos descritos para a aplicação de amostra sonora de Visão de Computador.

Por exemplo, para clonar novamente o repositório para a aplicação face sample a partir de um pedido de comando, executar o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Abra e construa a app de amostras no Visual Studio

Primeiro, tem de construir a aplicação da amostra, para que o Visual Studio possa resolver as dependências, antes de poder executar ou explorar a aplicação da amostra. Para abrir e construir a aplicação de amostra, faça os seguintes passos:

1. Abra o ficheiro de solução Visual `/Sample-WPF/VisionAPI-WPF-Samples.sln` Studio, no Estúdio Visual.
1. Certifique-se de que a solução Visual Studio contém dois projetos:  

   * SampleUserControlLibrary
   * Amostras visionAPI-WPF  

   Se o projeto SampleUserControlLibrary não estiver disponível, confirme que clonou novamente o `Microsoft/Cognitive-Vision-Windows` repositório.
1. No Visual Studio, prima Ctrl+Shift+B ou escolha **Construir** a partir do menu de fita e, em seguida, escolha **Build Solution** para construir a solução.

## <a name="run-and-interact-with-the-sample-app"></a>Corra e interaja com a aplicação da amostra

Pode executar a aplicação de amostra, para ver como interage consigo e com a biblioteca de clientes da Visão de Computador ao executar várias tarefas, como gerar miniaturas ou marcar imagens. Para correr e interagir com a aplicação da amostra, faça os seguintes passos:

1. Depois de concluída a construção, prima **F5** ou escolha **Debug** no menu de fita e, em seguida, escolha **Iniciar a depurar** para executar a aplicação da amostra.
1. Quando a aplicação da amostra for apresentada, escolha **a Gestão** da Chave de Subscrição no painel de navegação para exibir a página de Gestão da Chave de Assinatura.
   ![Página de Gestão de Chave de Subscrição](../Images/Vision_UI_Subscription.PNG)  
1. Introduza a sua chave de subscrição na **Chave de Subscrição**.
1. Introduza o URL de ponto final em **Endpoint**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Se não quiser introduzir a sua chave de subscrição e URL de ponto final da próxima vez que executar a aplicação da amostra, escolha **Guardar definição** para guardar a chave de subscrição e URL de ponto final para o seu computador. Se pretender eliminar a sua chave de subscrição previamente guardada e URL de ponto final, escolha **Apagar Definição**.

   > [!NOTE]
   > A aplicação da amostra utiliza armazenamento isolado `System.IO.IsolatedStorage` e, para armazenar a sua chave de subscrição e URL de ponto final.

1. Em **Seleção de um cenário** no painel de navegação, selecione um dos cenários atualmente incluídos com a aplicação da amostra:  

   | Cenário | Descrição |
   |----------|-------------|
   |Analisar Imagem | Utiliza a operação ['Imagem de Análise'](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para analisar uma imagem local ou remota. Pode escolher as características visuais e a linguagem para a análise, e ver tanto a imagem como os resultados.  |
   |Analisar imagem com modelo de domínio | Utiliza a operação [De Modelos Específicos de Domínio lista](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) para listar os modelos de domínio a partir dos quais pode selecionar e a operação de Conteúdo Específico do Domínio de [Reconhecimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) para analisar uma imagem local ou remota utilizando o modelo de domínio selecionado. Também pode escolher o idioma para a análise. |
   |Descrever imagem | Utiliza a operação [Descrever imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) para criar uma descrição legível pelo homem de uma imagem local ou remota. Também pode escolher o idioma para a descrição. |
   |Gerar Tags | Utiliza a operação [Imagem de Marcação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) para marcar as características visuais de uma imagem local ou remota. Também pode escolher o idioma utilizado para as etiquetas. |
   |Reconhecer Texto (OCR) | Utiliza a [operação OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para reconhecer e extrair texto impresso a partir de uma imagem. Pode escolher o idioma a utilizar ou deixar a Visão de Computador detetar automaticamente o idioma. |
   |Reconhecer Texto V2 (Inglês) | Utiliza o [texto de reconhecimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) e obtém operações de resultado de [operação de texto para](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) reconhecer e extrair texto impresso ou manuscrito de uma imagem. |
   |Obter Miniatura | Utiliza a operação [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) para gerar uma miniatura para uma imagem local ou remota. |

   A imagem a seguir ilustra a página fornecida para o cenário de Imagem de Análise, depois de analisar uma imagem de amostra.
   ![Screenshot da página de imagem de Analisar](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Explore a aplicação da amostra

A solução Visual Studio para a aplicação de amostras de Visão De Computador contém dois projetos:

* SampleUserControlLibrary  
  O projeto SampleUserControlLibrary fornece funcionalidade partilhada por várias amostras de Serviços Cognitivos. O projeto contém o seguinte:
  * AmostraScenarios  
    Um UserControl que fornece uma apresentação padronizada, como a barra de título, painel de navegação e painel de conteúdo, para amostras. A aplicação de amostra de Visão de Computador utiliza este controlo na janela MainWindow.xaml para exibir páginas de cenário e aceder a informações partilhadas em cenários, como a chave de subscrição e URL de ponto final.
  * SubscriçãoKeyPage  
    Uma página que fornece um layout padronizado para introduzir uma chave de subscrição e URL de ponto final para a aplicação da amostra. A aplicação de amostra de Visão de Computador utiliza esta página para gerir a chave de subscrição e URL de ponto final utilizado pelas páginas de cenário.
  * VideoResultControl  
    Um UserControl que fornece uma apresentação padronizada para informações de vídeo. A aplicação de amostras computer vision não utiliza este controlo.
* Amostras visionAPI-WPF  
  O principal projeto para a aplicação de amostra sonora de Visão De Computador, este projeto contém toda a funcionalidade interessante para visão de computador. O projeto contém o seguinte:
  * AnalisarInDomainPage.xaml  
    A página de cenário para o cenário de Imagem de Análise com Modelo de Domínio.
  * AnalisarImage.xaml  
    A página de cenário para o cenário de Imagem de Análise.
  * DescreverPage.xaml  
    A página de cenário para o cenário de Imagem de Descrever.
  * ImageScenarioPage.cs  
    A aula imageScenarioPage, a partir da qual são derivadas todas as páginas de cenário na aplicação da amostra. Esta classe gere a funcionalidade, como fornecer credenciais e resultados de formatação, partilhados por todas as páginas de cenário.
  * MainWindow.xaml  
    A janela principal para a aplicação da amostra, utiliza o controlo SampleScenarios para apresentar o SubscriptionKeyPage e as páginas de cenário.
  * OCRPage.xaml  
    A página de cenário para o cenário do Texto Reconhecido (OCR).
  * RecognizeLanguage.cs  
    A classe RecogniseLanguage, que fornece informações sobre os idiomas suportados pelos vários métodos da aplicação da amostra.
  * TagsPage.xaml  
    A página de cenário para o cenário De gerar Tags.
  * TextRecognitionPage.xaml  
    A página de cenário para o cenário reconhecer o texto V2 (inglês).
  * ThumbnailPage.xaml  
    A página de cenário para o cenário Get Thumbnail.

### <a name="explore-the-sample-code"></a>Explore o código de amostra

As principais porções do código de amostra são enquadradas com blocos de comentários que começam `KEY SAMPLE CODE STARTS HERE` com e terminam com `KEY SAMPLE CODE ENDS HERE` , para facilitar a exploração da aplicação da amostra. Estas porções-chave do código de amostra contêm o código mais relevante para aprender a usar a biblioteca de clientes da API da Visão De Computador para fazer várias tarefas. Pode pesquisar `KEY SAMPLE CODE STARTS HERE` no Estúdio Visual para mover-se entre as secções de código mais relevantes na aplicação de amostras de Visão de Computador. 

Por exemplo, o `UploadAndAnalyzeImageAsync` método, mostrado a seguir e incluído no AnalyzePage.xaml, demonstra como usar a biblioteca do cliente para analisar uma imagem local invocando o `ComputerVisionClient.AnalyzeImageInStreamAsync` método.

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

### <a name="explore-the-client-library"></a>Explore a biblioteca do cliente

Esta aplicação de amostra utiliza a biblioteca de clientes da API da Visão de Computador, um invólucro fino do cliente C# para a API de Visão De Computador em Serviços Cognitivos Azure. A biblioteca cliente está disponível a partir do NuGet no pacote [Microsoft.Azure.CognitiveServices.Vision.ComputerVision.](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) Quando construiu a aplicação Visual Studio, recuperou a biblioteca do cliente a partir do seu pacote NuGet correspondente. Também pode ver o código fonte da biblioteca do cliente na `/ClientLibrary` pasta do `Microsoft/Cognitive-Vision-Windows` repositório.

A funcionalidade da biblioteca do cliente centra-se em torno da `ComputerVisionClient` classe, no espaço de `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` nomes, enquanto os modelos utilizados pela `ComputerVisionClient` classe ao interagir com a Visão De Computador são encontrados no espaço de `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` nomes. Nas várias páginas de cenário XAML incluídas na aplicação da amostra, encontrará as `using` seguintes diretivas para esses espaços de nome:

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

Você vai aprender mais sobre os vários métodos incluídos com a `ComputerVisionClient` classe à medida que você explora os cenários incluídos com a aplicação de amostra de Visão de Computador.

## <a name="explore-the-analyze-image-scenario"></a>Explore o cenário de Imagem de Análise

Este cenário é gerido pela página AnalyzePage.xaml. Pode escolher as características visuais e a linguagem para a análise, e ver tanto a imagem como os resultados. A página do cenário fá-lo utilizando um dos seguintes métodos, dependendo da origem da imagem:

* UploadAndAnalyzeImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.AnalyzeImageInStreamAsync` método.
* AnalyzeUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.AnalyzeImageAsync` método.

O `UploadAndAnalyzeImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como um , em `Stream` seguida, obtém as funcionalidades visuais e o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, passando o `Stream` para o arquivo, as características visuais, e o idioma, em seguida, devolve o resultado como um `ImageAnalysis` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `AnalyzeUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Obtém as características visuais e o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageInStreamAsync` método, passando o URL de imagem, as características visuais e a linguagem, e depois devolve o resultado como um `ImageAnalysis` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Explore a imagem de análise com o cenário do modelo de domínio

Este cenário é gerido pela página AnalyzeInDomainPage.xaml. Pode escolher um modelo de domínio, como `celebrities` `landmarks` ou, e linguagem para realizar uma análise específica do domínio da imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* Obter DisponíveisDomainModelsAsync  
  Este método obtém a lista de modelos de domínio disponíveis a partir da Visão de Computador e povoa o `_domainModelComboBox` controlo ComboBox na página, utilizando o `ComputerVisionClient.ListModelsAsync` método.
* UploadAndAnalyzeInDomainImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` método.
* AnáliseInDomainUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.AnalyzeImageByDomainAsync` método.

O `UploadAndAnalyzeInDomainImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como um , em `Stream` seguida, obtém o idioma selecionado na página de cenário. Chama o `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` método, passando o `Stream` para o arquivo, o nome do modelo de domínio, e a língua, em seguida, devolve o resultado como um `DomainModelResults` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `AnalyzeInDomainUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.AnalyzeImageByDomainAsync` método, passando o URL de imagem, as características visuais e a linguagem, e depois devolve o resultado como um `DomainModelResults` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-describe-image-scenario"></a>Explore o cenário de imagem de descreveção

Este cenário é gerido pela página DescreverPage.xaml. Pode escolher uma linguagem para criar uma descrição legível pelo homem da imagem e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndDescribeImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.DescribeImageInStreamAsync` método.
* DescreverUrAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.DescribeImageAsync` método.

O `UploadAndDescribeImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como um , em `Stream` seguida, obtém o idioma selecionado na página de cenário. Chama o `ComputerVisionClient.DescribeImageInStreamAsync` método, passando o `Stream` para o arquivo, o número máximo de candidatos (neste caso, 3), e a língua, em seguida, devolve o resultado como um `ImageDescription` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `DescribeUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.DescribeImageAsync` método, passando o URL de imagem, o número máximo de candidatos (neste caso, 3), e a língua, e depois devolve o resultado como `ImageDescription` um caso. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-generate-tags-scenario"></a>Explore o cenário de Gerar Tags

Este cenário é gerido pela página TagsPage.xaml. Pode escolher um idioma para marcar as características visuais de uma imagem e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndGetTagsForImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.TagImageInStreamAsync` método.
* GenerateTagsForUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.TagImageAsync` método.

O `UploadAndGetTagsForImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como um , em `Stream` seguida, obtém o idioma selecionado na página de cenário. Chama o `ComputerVisionClient.TagImageInStreamAsync` método, passando o `Stream` para o arquivo e o idioma, e depois devolve o resultado como um `TagResult` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `GenerateTagsForUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.TagImageAsync` método, passando o URL de imagem e a linguagem, e depois devolve o resultado como `TagResult` um exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Explore o cenário de Reconhecer Texto (OCR)

Este cenário é gerido pela página OCRPage.xaml. Pode escolher um idioma para reconhecer e extrair texto impresso a partir de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.RecognizePrintedTextInStreamAsync` método.
* ReconheceUrAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.RecognizePrintedTextAsync` método.

O `UploadAndRecognizeImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como um , em `Stream` seguida, obtém o idioma selecionado na página de cenário. Chama o `ComputerVisionClient.RecognizePrintedTextInStreamAsync` método, indicando que a orientação não é detetada e passando o `Stream` para o arquivo e o idioma, em seguida, devolve o resultado como um `OcrResult` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Obtém o idioma selecionado na página do cenário. Chama o `ComputerVisionClient.RecognizePrintedTextAsync` método, indicando que a orientação não é detetada e passando o URL de imagem e a língua, em seguida, devolve o resultado como um `OcrResult` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Explore o cenário reconhecer o texto V2 (inglês)

Este cenário é gerido pela página TextRecognitionPage.xaml. Pode escolher o modo de reconhecimento e um idioma para reconhecer e extrair assíncroticamente texto impresso ou manuscrito a partir de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para a Visão Computacional, chamando o método e `RecognizeAsync` passando um delegado parametrizado para o `ComputerVisionClient.RecognizeTextInStreamAsync` método.
* ReconheceUrAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão Computacional, chamando o `RecognizeAsync` método e passando um delegado parametrizado para o `ComputerVisionClient.RecognizeTextAsync` método.
* ReconhecerAync Este método trata do pedido assíncronos, tanto para os métodos como para os `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` métodos, bem como para as sondagens para obter resultados, chamando o `ComputerVisionClient.GetTextOperationResultAsync` método.

Ao contrário dos outros cenários incluídos na aplicação da amostra de Visão De Computador, este cenário é assíncrona, neste método é chamado para iniciar o processo, mas um método diferente é chamado para verificar o estado e devolver os resultados desse processo. O fluxo lógico neste cenário é um pouco diferente do que nos outros cenários.

O `UploadAndRecognizeImageAsync` método abre o ficheiro local especificado para leitura como um , em `imageFilePath` `Stream` seguida, chama o `RecognizeAsync` método, passando:

* Uma expressão lambda para um delegado assíncronos parametrizado do `ComputerVisionClient.RecognizeTextInStreamAsync` método, com o `Stream` para o arquivo e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc` .
* Uma expressão lambda para um delegado obter o valor do `Operation-Location` cabeçalho de resposta, em `GetOperationUrlFunc` .

O `RecognizeUrlAsync` método chama o `RecognizeAsync` método, passando:

* Uma expressão lambda para um delegado assíncronos parametrizado do `ComputerVisionClient.RecognizeTextAsync` método, com o URL da imagem remota e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc` .
* Uma expressão lambda para um delegado obter o valor do `Operation-Location` cabeçalho de resposta, em `GetOperationUrlFunc` .

Quando o `RecognizeAsync` método estiver concluído, tanto os métodos como os `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` métodos devolvem o resultado como `TextOperationResult` exemplo. Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeAsync` método chama o delegado parametrizado para o ou método passado e aguarda a `ComputerVisionClient.RecognizeTextInStreamAsync` `ComputerVisionClient.RecognizeTextAsync` `GetHeadersAsyncFunc` resposta. O método chama então o delegado passado `GetOperationUrlFunc` para obter o valor do `Operation-Location` cabeçalho de resposta da resposta. Este valor é o URL utilizado para recuperar os resultados do método passado a `GetHeadersAsyncFunc` partir da Visão de Computador.

Em `RecognizeAsync` seguida, o método chama o `ComputerVisionClient.GetTextOperationResultAsync` método, passando o URL recuperado do cabeçalho de `Operation-Location` resposta, para obter o estado e o resultado do método passado em `GetHeadersAsyncFunc` . Se o estado não indicar que o método foi concluído, com ou sem sucesso, o `RecognizeAsync` método chama `ComputerVisionClient.GetTextOperationResultAsync` mais 3 vezes, esperando 3 segundos entre chamadas. O `RecognizeAsync` método devolve os resultados ao método que o chamou.

## <a name="explore-the-get-thumbnail-scenario"></a>Explore o cenário Get Thumbnail

Este cenário é gerido pela página ThumbnailPage.xaml. Pode indicar se deve utilizar a colheita inteligente e especificar a altura e largura desejadas, para gerar uma miniatura a partir de uma imagem, e ver tanto a imagem como os resultados. A página do cenário utiliza os seguintes métodos, dependendo da origem da imagem:

* UploadAndThumbnailImageAsync  
  Este método é utilizado para imagens locais, nas quais a imagem deve ser codificada como a `Stream` e enviada para Visão Computacional, chamando o `ComputerVisionClient.GenerateThumbnailInStreamAsync` método.
* MiniaturaUrlAsync  
  Este método é utilizado para imagens remotas, nas quais o URL para a imagem é enviado para Visão De Computador, chamando o `ComputerVisionClient.GenerateThumbnailAsync` método.

O `UploadAndThumbnailImageAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Como a aplicação da amostra está a analisar uma imagem local, tem de enviar o conteúdo dessa imagem para a Visão De Computador. Abre o ficheiro local especificado `imageFilePath` para leitura como `Stream` . Chama o `ComputerVisionClient.GenerateThumbnailInStreamAsync` método, passando a largura, a altura, o `Stream` para o ficheiro, e se deve usar a colheita inteligente, e depois devolve o resultado como um `Stream` . Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

O `RecognizeUrlAsync` método cria uma nova `ComputerVisionClient` instância, utilizando a chave de subscrição especificada e URL de ponto final. Chama o `ComputerVisionClient.GenerateThumbnailAsync` método, passando a largura, a altura, o URL para a imagem, e se usar a colheita inteligente, em seguida, devolve o resultado como um `Stream` . Os métodos herdados da `ImageScenarioPage` classe apresentam os resultados devolvidos na página do cenário.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine a pasta na qual clonou o `Microsoft/Cognitive-Vision-Windows` repositório. Se optou por utilizar as imagens da amostra, também elimine a pasta na qual clonou o `Microsoft/Cognitive-Face-Windows` repositório.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o serviço Face](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
