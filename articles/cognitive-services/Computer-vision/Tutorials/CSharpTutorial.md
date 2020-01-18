---
title: 'Exemplo: explorar um aplicativo de processamento de imagens noC#'
titleSuffix: Azure Cognitive Services
description: Explore um aplicativo básico do Windows que usa o API da Pesquisa Visual Computacional nos serviços cognitivas do Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168870"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Exemplo: explorar um aplicativo de processamento de imagens comC#

Explore um aplicativo básico do Windows que usa Pesquisa Visual Computacional para executar o OCR (reconhecimento óptico de caracteres), criar miniaturas cortadas com inteligência, além de detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. O exemplo abaixo permite submeter um URL de imagem ou um ficheiro armazenado localmente. Você pode usar esse exemplo de software livre como um modelo para criar seu próprio aplicativo para Windows usando o API da Pesquisa Visual Computacional e o Windows Presentation Foundation (WPF), uma parte do .NET Framework.

> [!div class="checklist"]
> * Obter o aplicativo de exemplo do GitHub
> * Abrir e compilar o aplicativo de exemplo no Visual Studio
> * Execute o aplicativo de exemplo e interaja com ele para executar vários cenários
> * Explore os vários cenários incluídos no aplicativo de exemplo

## <a name="prerequisites"></a>Pré-requisitos

Antes de explorar o aplicativo de exemplo, verifique se você atendeu aos seguintes pré-requisitos:

* Tem de ter o [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) ou posterior.
* Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de avaliação gratuita de [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional e obter sua chave. Anote também a URL do ponto de extremidade do serviço.

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

O aplicativo de exemplo Pesquisa Visual Computacional está disponível no GitHub no repositório `Microsoft/Cognitive-Vision-Windows`. Esse repositório também inclui o repositório `Microsoft/Cognitive-Common-Windows` como um submódulo git. Você pode clonar recursivamente esse repositório, incluindo o submódulo, usando o comando `git clone --recurse-submodules` da linha de comando ou usando a área de trabalho do GitHub.

Por exemplo, para clonar recursivamente o repositório para o aplicativo de exemplo Pesquisa Visual Computacional de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Não baixe este repositório como um ZIP. O Git não inclui submódulos ao baixar um repositório como um ZIP.

### <a name="get-optional-sample-images"></a>Obter imagens de exemplo opcionais

Opcionalmente, você pode usar as imagens de exemplo incluídas no aplicativo de exemplo de [face](../../Face/Overview.md) , disponível no GitHub no repositório `Microsoft/Cognitive-Face-Windows`. Esse aplicativo de exemplo inclui uma pasta, `/Data`, que contém várias imagens de pessoas. Você pode clonar recursivamente esse repositório, também, pelos métodos descritos para o aplicativo de exemplo Pesquisa Visual Computacional.

Por exemplo, para clonar recursivamente o repositório para o aplicativo de exemplo de face de um prompt de comando, execute o seguinte comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Abrir e compilar o aplicativo de exemplo no Visual Studio

Primeiro, você deve criar o aplicativo de exemplo para que o Visual Studio possa resolver dependências, antes de poder executar ou explorar o aplicativo de exemplo. Para abrir e compilar o aplicativo de exemplo, execute as seguintes etapas:

1. Abra o arquivo de solução do Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`no Visual Studio.
1. Verifique se a solução do Visual Studio contém dois projetos:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-exemplos  

   Se o projeto SampleUserControlLibrary não estiver disponível, confirme que você cloneu recursivamente o repositório `Microsoft/Cognitive-Vision-Windows`.
1. No Visual Studio, pressione Ctrl + Shift + B ou escolha **Compilar** no menu faixa de opções e escolha **Compilar solução** para compilar a solução.

## <a name="run-and-interact-with-the-sample-app"></a>Executar e interagir com o aplicativo de exemplo

Você pode executar o aplicativo de exemplo para ver como ele interage com você e com a biblioteca de cliente Pesquisa Visual Computacional ao executar várias tarefas, como gerar miniaturas ou imagens de marcação. Para executar e interagir com o aplicativo de exemplo, execute as seguintes etapas:

1. Após a conclusão da compilação, pressione **F5** ou escolha **depurar** no menu da faixa de opções e escolha **Iniciar Depuração** para executar o aplicativo de exemplo.
1. Quando o aplicativo de exemplo for exibido, escolha **Gerenciamento de chaves de assinatura** no painel de navegação para exibir a página de gerenciamento de chaves de assinatura.
   página de gerenciamento de chaves de assinatura ![](../Images/Vision_UI_Subscription.PNG)  
1. Insira sua chave de assinatura na **chave de assinatura**.
1. Insira a URL do ponto de extremidade no **ponto de extremidade**.  
   Por exemplo, se você estiver usando a chave de assinatura do Pesquisa Visual Computacional avaliação gratuita, insira a URL do ponto de extremidade a seguir: `https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Se você não quiser inserir sua chave de assinatura e URL de ponto de extremidade na próxima vez que executar o aplicativo de exemplo, escolha **salvar configuração** para salvar a chave de assinatura e a URL do ponto de extremidade em seu computador. Se você quiser excluir a chave de assinatura e a URL de ponto de extremidade salvas anteriormente, escolha **Excluir configuração**.

   > [!NOTE]
   > O aplicativo de exemplo usa armazenamento isolado e `System.IO.IsolatedStorage`para armazenar a chave de assinatura e a URL do ponto de extremidade.

1. Em **selecionar um cenário** no painel de navegação, selecione um dos cenários atualmente incluídos com o aplicativo de exemplo:  

   | Cenário | Descrição |
   |----------|-------------|
   |Analisar imagem | Usa a operação de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para analisar uma imagem local ou remota. Você pode escolher os recursos visuais e o idioma da análise e ver a imagem e os resultados.  |
   |Analisar imagem com modelo de domínio | Usa a operação [listar modelos específicos de domínio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) para listar os modelos de domínio dos quais você pode selecionar e a operação [reconhecer conteúdo específico do domínio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) para analisar uma imagem local ou remota usando o modelo de domínio selecionado. Você também pode escolher o idioma para a análise. |
   |Descrever imagem | Usa a operação [descrever imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) para criar uma descrição legível de uma imagem local ou remota. Você também pode escolher o idioma para a descrição. |
   |Gerar marcas | Usa a operação [tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) para marcar os recursos visuais de uma imagem local ou remota. Você também pode escolher o idioma usado para as marcas. |
   |Reconhecimento de Texto (OCR) | Usa a operação de [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para reconhecer e extrair texto impresso de uma imagem. Você pode escolher o idioma a ser usado ou permitir que Pesquisa Visual Computacional detectar automaticamente o idioma. |
   |Reconhecimento de Texto v2 (inglês) | Usa as operações de resultado de operação [reconhecimento de texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) e [Get reconhecimento de texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) para reconhecê-las de forma assíncrona e extrair texto impresso ou manuscrito de uma imagem. |
   |Obter miniatura | Usa a operação [obter miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) para gerar uma miniatura para uma imagem local ou remota. |

   A captura de tela a seguir ilustra a página fornecida para o cenário analisar imagem, depois de analisar uma imagem de exemplo.
   ![captura de tela da página analisar imagem](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Explorar o aplicativo de exemplo

A solução do Visual Studio para o aplicativo de exemplo Pesquisa Visual Computacional contém dois projetos:

* SampleUserControlLibrary  
  O projeto SampleUserControlLibrary fornece funcionalidade compartilhada por vários exemplos de serviços cognitivas. O projeto contém o seguinte:
  * SampleScenarios  
    Um UserControl que fornece uma apresentação padronizada, como a barra de título, o painel de navegação e o painel de conteúdo, para obter exemplos. O aplicativo de exemplo Pesquisa Visual Computacional usa esse controle na janela MainWindow. XAML para exibir páginas de cenário e acessar informações compartilhadas entre cenários, como a chave de assinatura e a URL do ponto de extremidade.
  * SubscriptionKeyPage  
    Uma página que fornece um layout padronizado para inserir uma chave de assinatura e uma URL de ponto de extremidade para o aplicativo de exemplo. O aplicativo de exemplo Pesquisa Visual Computacional usa essa página para gerenciar a chave de assinatura e a URL do ponto de extremidade usados pelas páginas do cenário.
  * VideoResultControl  
    Um UserControl que fornece uma apresentação padronizada para informações de vídeo. O aplicativo de exemplo Pesquisa Visual Computacional não usa esse controle.
* VisionAPI-WPF-exemplos  
  O projeto principal para o aplicativo de exemplo Pesquisa Visual Computacional, este projeto contém toda a funcionalidade interessante para Pesquisa Visual Computacional. O projeto contém o seguinte:
  * AnalyzeInDomainPage. XAML  
    A página cenário para o cenário analisar imagem com modelo de domínio.
  * AnalyzeImage. XAML  
    A página cenário do cenário de análise de imagem.
  * DescribePage.xaml  
    A página cenário do cenário descrever imagem.
  * ImageScenarioPage.cs  
    A classe ImageScenarioPage, da qual todas as páginas de cenário no aplicativo de exemplo são derivadas. Essa classe gerencia a funcionalidade, como fornecer credenciais e saída de formatação, compartilhada por todas as páginas de cenário.
  * MainWindow.xaml  
    A janela principal para o aplicativo de exemplo, ela usa o controle SampleScenarios para apresentar as páginas SubscriptionKeyPage e Scenario.
  * OCRPage. XAML  
    A página cenário do cenário de Reconhecimento de Texto (OCR).
  * RecognizeLanguage.cs  
    A classe RecognizeLanguage, que fornece informações sobre os idiomas com suporte pelos vários métodos no aplicativo de exemplo.
  * TagsPage.xaml  
    A página cenário para o cenário gerar marcas.
  * TextRecognitionPage.xaml  
    A página cenário para o cenário de Reconhecimento de Texto v2 (inglês).
  * ThumbnailPage.xaml  
    A página cenário para o cenário obter miniatura.

### <a name="explore-the-sample-code"></a>Explore o código de exemplo

As principais partes do código de exemplo são emolduradas com blocos de comentário que começam com `KEY SAMPLE CODE STARTS HERE` e terminam com `KEY SAMPLE CODE ENDS HERE`, para facilitar a exploração do aplicativo de exemplo. Essas partes-chave do código de exemplo contêm o código mais relevante para aprender a usar a biblioteca de cliente API da Pesquisa Visual Computacional para realizar várias tarefas. Você pode pesquisar `KEY SAMPLE CODE STARTS HERE` no Visual Studio para se mover entre as seções mais relevantes do código no aplicativo de exemplo Pesquisa Visual Computacional. 

Por exemplo, o método `UploadAndAnalyzeImageAsync`, mostrado a seguir e incluído em AnalyzePage. XAML, demonstra como usar a biblioteca de cliente para analisar uma imagem local invocando o método `ComputerVisionClient.AnalyzeImageInStreamAsync`.

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

### <a name="explore-the-client-library"></a>Explorar a biblioteca de cliente

Este aplicativo de exemplo usa a biblioteca de cliente API da Pesquisa Visual Computacional, C# um wrapper de cliente fino para o API da pesquisa Visual computacional nos serviços cognitivas do Azure. A biblioteca de cliente está disponível no NuGet no pacote [Microsoft. Azure. cognitivaservices. Vision. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) . Ao criar o aplicativo do Visual Studio, você recuperou a biblioteca do cliente do seu pacote NuGet correspondente. Você também pode exibir o código-fonte da biblioteca de cliente na pasta `/ClientLibrary` do repositório `Microsoft/Cognitive-Vision-Windows`.

A funcionalidade da biblioteca de cliente é centrada na classe `ComputerVisionClient`, no namespace `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`, enquanto os modelos usados pela classe `ComputerVisionClient` ao interagir com Pesquisa Visual Computacional são encontrados no namespace `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. Nas várias páginas do cenário XAML incluídas no aplicativo de exemplo, você encontrará as seguintes diretivas de `using` para esses namespaces:

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

Você aprenderá mais sobre os vários métodos incluídos na classe `ComputerVisionClient` ao explorar os cenários incluídos no aplicativo de exemplo Pesquisa Visual Computacional.

## <a name="explore-the-analyze-image-scenario"></a>Explore o cenário de análise de imagem

Esse cenário é gerenciado pela página AnalyzePage. XAML. Você pode escolher os recursos visuais e o idioma da análise e ver a imagem e os resultados. A página cenário faz isso usando um dos seguintes métodos, dependendo da origem da imagem:

* UploadAndAnalyzeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.AnalyzeImageAsync`.

O método `UploadAndAnalyzeImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, obtém os recursos visuais e o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando o `Stream` para o arquivo, os recursos visuais e o idioma, em seguida, retorna o resultado como uma instância de `ImageAnalysis`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `AnalyzeUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele obtém os recursos visuais e o idioma selecionados na página cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando a URL da imagem, os recursos visuais e o idioma e, em seguida, retorna o resultado como uma instância de `ImageAnalysis`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Explore o cenário de análise de imagem com modelo de domínio

Esse cenário é gerenciado pela página AnalyzeInDomainPage. XAML. Você pode escolher um modelo de domínio, como `celebrities` ou `landmarks`, e o idioma para executar uma análise específica de domínio da imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* GetAvailableDomainModelsAsync  
  Esse método obtém a lista de modelos de domínio disponíveis de Pesquisa Visual Computacional e popula o `_domainModelComboBox` controle ComboBox na página, usando o método `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.AnalyzeImageByDomainAsync`.

O método `UploadAndAnalyzeInDomainImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`, passando o `Stream` para o arquivo, o nome do modelo de domínio e o idioma e, em seguida, retorna o resultado como uma instância de `DomainModelResults`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `AnalyzeInDomainUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.AnalyzeImageByDomainAsync`, passando a URL da imagem, os recursos visuais e o idioma e, em seguida, retorna o resultado como uma instância de `DomainModelResults`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="explore-the-describe-image-scenario"></a>Explore o cenário descrever imagem

Esse cenário é gerenciado pela página DescribePage. XAML. Você pode escolher um idioma para criar uma descrição legível da imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndDescribeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.DescribeImageAsync`.

O método `UploadAndDescribeImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.DescribeImageInStreamAsync`, passando o `Stream` para o arquivo, o número máximo de candidatos (nesse caso, 3) e o idioma, em seguida, retorna o resultado como uma instância de `ImageDescription`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `DescribeUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.DescribeImageAsync`, passando a URL da imagem, o número máximo de candidatos (nesse caso, 3) e o idioma, em seguida, retorna o resultado como uma instância de `ImageDescription`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="explore-the-generate-tags-scenario"></a>Explorar o cenário de geração de marcas

Esse cenário é gerenciado pela página TagsPage. XAML. Você pode escolher um idioma para marcar os recursos visuais de uma imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndGetTagsForImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.TagImageAsync`.

O método `UploadAndGetTagsForImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.TagImageInStreamAsync`, passando o `Stream` para o arquivo e o idioma e, em seguida, retorna o resultado como uma instância de `TagResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `GenerateTagsForUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.TagImageAsync`, passando a URL da imagem e o idioma e, em seguida, retorna o resultado como uma instância de `TagResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Explore o cenário de Reconhecimento de Texto (OCR)

Esse cenário é gerenciado pela página OCRPage. XAML. Você pode escolher um idioma para reconhecer e extrair o texto impresso de uma imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.RecognizePrintedTextAsync`.

O método `UploadAndRecognizeImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.RecognizePrintedTextInStreamAsync`, indicando que a orientação não é detectada e passando o `Stream` para o arquivo e o idioma, em seguida, retorna o resultado como uma instância de `OcrResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `RecognizeUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele obtém o idioma selecionado na página cenário. Ele chama o método `ComputerVisionClient.RecognizePrintedTextAsync`, indicando que a orientação não é detectada e passando a URL da imagem e o idioma, em seguida, retorna o resultado como uma instância de `OcrResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Explore o cenário do Reconhecimento de Texto v2 (inglês)

Esse cenário é gerenciado pela página TextRecognitionPage. XAML. Você pode escolher o modo de reconhecimento e um idioma para reconhecer e extrair de forma assíncrona o texto impresso ou manuscrito de uma imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndRecognizeImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como uma `Stream` e enviada para Pesquisa Visual Computacional chamando o método `RecognizeAsync` e passando um delegado parametrizado para o método `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `RecognizeAsync` e passando um delegado parametrizado para o método `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync esse método manipula a chamada assíncrona para os métodos `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync`, além de sondar os resultados chamando o método `ComputerVisionClient.GetTextOperationResultAsync`.

Ao contrário dos outros cenários incluídos no aplicativo de exemplo Pesquisa Visual Computacional, esse cenário é assíncrono, no caso de um método ser chamado para iniciar o processo, mas um método diferente é chamado para verificar o status e retornar os resultados desse processo. O fluxo lógico nesse cenário é um pouco diferente do que nos outros cenários.

O método `UploadAndRecognizeImageAsync` abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`, em seguida, chama o método `RecognizeAsync`, passando:

* Uma expressão lambda para um delegado assíncrono parametrizado do método `ComputerVisionClient.RecognizeTextInStreamAsync`, com o `Stream` para o arquivo e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado para obter o valor do cabeçalho de resposta `Operation-Location`, em `GetOperationUrlFunc`.

O método `RecognizeUrlAsync` chama o método `RecognizeAsync`, passando:

* Uma expressão lambda para um delegado assíncrono parametrizado do método `ComputerVisionClient.RecognizeTextAsync`, com a URL da imagem remota e o modo de reconhecimento como parâmetros, em `GetHeadersAsyncFunc`.
* Uma expressão lambda para um delegado para obter o valor do cabeçalho de resposta `Operation-Location`, em `GetOperationUrlFunc`.

Quando o método de `RecognizeAsync` é concluído, os métodos `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` retornam o resultado como uma instância de `TextOperationResult`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `RecognizeAsync` chama o delegado parametrizado para o método `ComputerVisionClient.RecognizeTextInStreamAsync` ou `ComputerVisionClient.RecognizeTextAsync` passado em `GetHeadersAsyncFunc` e aguarda a resposta. Em seguida, o método chama o delegado passado em `GetOperationUrlFunc` para obter o valor do cabeçalho de resposta `Operation-Location` da resposta. Esse valor é a URL usada para recuperar os resultados do método passado em `GetHeadersAsyncFunc` de Pesquisa Visual Computacional.

Em seguida, o método `RecognizeAsync` chama o método `ComputerVisionClient.GetTextOperationResultAsync`, passando a URL recuperada do cabeçalho de resposta `Operation-Location`, para obter o status e o resultado do método passado em `GetHeadersAsyncFunc`. Se o status não indicar que o método foi concluído, com êxito ou sem êxito, o método `RecognizeAsync` chamará `ComputerVisionClient.GetTextOperationResultAsync` mais três vezes, aguardando 3 segundos entre as chamadas. O método `RecognizeAsync` retorna os resultados para o método que o chamou.

## <a name="explore-the-get-thumbnail-scenario"></a>Explore o cenário obter miniatura

Esse cenário é gerenciado pela página ThumbnailPage. XAML. Você pode indicar se deseja usar o corte inteligente e especificar a altura e a largura desejadas, para gerar uma miniatura de uma imagem e ver a imagem e os resultados. A página cenário usa os seguintes métodos, dependendo da origem da imagem:

* UploadAndThumbnailImageAsync  
  Esse método é usado para imagens locais, nas quais a imagem deve ser codificada como um `Stream` e enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Esse método é usado para imagens remotas, nas quais a URL para a imagem é enviada para Pesquisa Visual Computacional chamando o método `ComputerVisionClient.GenerateThumbnailAsync`.

O método `UploadAndThumbnailImageAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Como o aplicativo de exemplo está analisando uma imagem local, ele precisa enviar o conteúdo dessa imagem para Pesquisa Visual Computacional. Ele abre o arquivo local especificado em `imageFilePath` para leitura como um `Stream`. Ele chama o método `ComputerVisionClient.GenerateThumbnailInStreamAsync`, passando a largura, a altura, a `Stream` do arquivo e se deseja usar o corte inteligente e, em seguida, retorna o resultado como um `Stream`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

O método `RecognizeUrlAsync` cria uma nova instância de `ComputerVisionClient`, usando a chave de assinatura especificada e a URL do ponto de extremidade. Ele chama o método `ComputerVisionClient.GenerateThumbnailAsync`, passando a largura, a altura, a URL da imagem e se deseja usar o corte inteligente e, em seguida, retorna o resultado como um `Stream`. Os métodos herdados da classe `ImageScenarioPage` apresentam os resultados retornados na página cenário.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua a pasta na qual você clonou o repositório de `Microsoft/Cognitive-Vision-Windows`. Se você optou por usar as imagens de exemplo, exclua também a pasta na qual você clonou o repositório de `Microsoft/Cognitive-Face-Windows`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução ao serviço facial](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
