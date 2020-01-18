---
title: 'Tutorial: detectar e exibir dados de face em uma imagem usando o SDK do .NET'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo do Windows que usa o serviço de face para detectar e estruturar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ab0ed56b953cf2c0d96fd2d91d9a3b09fddace72
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165926"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Tutorial: criar um aplicativo WPF (Windows Presentation Framework) para exibir dados de face em uma imagem

Neste tutorial, você aprenderá a usar o serviço de face do Azure, por meio do SDK do cliente .NET, para detectar rostos em uma imagem e, em seguida, apresentar os dados na interface do usuário. Você criará um aplicativo do WPF que detecta faces, desenha um quadro em cada face e exibe uma descrição da face na barra de status. 

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação WPF
> - Instalar a biblioteca de cliente facial
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado
> - Exibir uma descrição da face realçada na barra de status

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)

O código de exemplo completo está disponível no repositório de [exemplo de aparência de Csharp de cognitiva](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) no github.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 


## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura facial. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço de face e obter sua chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a cadeia de caracteres de ponto de extremidade de serviço e chave, denominada `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Siga estas etapas para criar um novo projeto de aplicativo WPF.

1. No Visual Studio, abra a caixa de diálogo novo projeto. Expanda **instalado**, **Visual C#** e, em seguida, selecione **aplicativo WPF (.NET Framework)** .
1. Dê à aplicação o nome **FaceTutorial** e, em seguida, clique em **OK**.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito do mouse no seu projeto na Gerenciador de Soluções e selecione **gerenciar pacotes NuGet**; em seguida, localize e instale o seguinte pacote:
    - [Microsoft. Azure. Cognitivaservices. Vision. facial 2.2.0-Preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Adicionar o código inicial

Nesta seção, você adicionará a estrutura básica do aplicativo sem seus recursos específicos de face.

### <a name="create-the-ui"></a>Criar a interface do usuário

Abra *MainWindow. XAML* e substitua o conteúdo pelo código a seguir&mdash;esse código cria a janela da interface do usuário. Os métodos `FacePhoto_MouseMove` e `BrowseButton_Click` são manipuladores de eventos que serão definidos posteriormente.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Criar a classe principal

Abra *MainWindow.XAML.cs* e adicione os namespaces da biblioteca de cliente, juntamente com outros namespaces necessários. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Em seguida, insira o código a seguir na classe **MainWindow** . Esse código cria uma instância de **FaceClient** usando a chave de assinatura e o ponto de extremidade.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Em seguida, adicione o construtor **MainWindow** . Ele verifica a cadeia de caracteres da URL do ponto de extremidade e a associa ao objeto do cliente.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Por fim, adicione os métodos **BrowseButton_Click** e **FacePhoto_MouseMove** à classe. Esses métodos correspondem aos manipuladores de eventos declarados em *MainWindow. XAML*. O método **BrowseButton_Click** cria um **OpenFileDialog**, que permite ao usuário selecionar uma imagem. jpg. Em seguida, ele exibe a imagem na janela principal. Você irá inserir o código restante para **BrowseButton_Click** e **FacePhoto_MouseMove** em etapas posteriores. Observe também a referência de `faceList`&mdash;uma lista de objetos **DetectedFace** . Essa referência é onde o aplicativo armazenará e chamará os dados reais de face.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Experimentar a aplicação

Prima **Iniciar** no menu para testar a sua aplicação. Quando a janela do aplicativo for aberta, clique em **procurar** no canto inferior esquerdo. Uma caixa de diálogo **Abrir arquivo** deve ser exibida. Selecione uma imagem do sistema de arquivos e verifique se ela é exibida na janela. Em seguida, feche o aplicativo e avance para a próxima etapa.

![Captura de ecrã que mostra uma imagem não modificada de rostos](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detectar rostos

Seu aplicativo irá detectar faces chamando o método **FaceClient. face. DetectWithStreamAsync** , que ENCAPSULA a API REST de [detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para carregar uma imagem local.

Insira o método a seguir na classe **MainWindow** abaixo do método **FacePhoto_MouseMove** . Esse método define uma lista de atributos de face para recuperar e ler o arquivo de imagem enviado em um **fluxo**. Em seguida, ele passa esses dois objetos para a chamada do método **DetectWithStreamAsync** .

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Desenhar retângulos em volta de rostos

Em seguida, você adicionará o código para desenhar um retângulo ao lado de cada face detectada na imagem. Na classe **MainWindow** , insira o código a seguir no final do método **BrowseButton_Click** , após a linha de `FacePhoto.Source = bitmapSource`. Esse código popula uma lista de faces detectadas da chamada para **UploadAndDetectFaces**. Em seguida, ele desenha um retângulo em volta de cada face e exibe a imagem modificada na janela principal.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Descrever as faces

Adicione o método a seguir à classe **MainWindow** abaixo do método **UploadAndDetectFaces** . Esse método converte os atributos de face recuperados em uma cadeia de caracteres que descreve a face.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Apresentar a descrição de rostos

Adicione o código a seguir ao método **FacePhoto_MouseMove** . Esse manipulador de eventos exibe a cadeia de caracteres de descrição facial em `faceDescriptionStatusBar` quando o cursor passa sobre um retângulo de rosto detectado.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem que tenha um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Você deve ver um retângulo vermelho em cada uma das faces na imagem. Se você mover o mouse sobre um retângulo de face, a descrição dessa face deverá aparecer na barra de status.

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu o processo básico para usar o SDK do .NET do serviço de face e criou um aplicativo para detectar e estruturar rostos em uma imagem. Em seguida, saiba mais sobre os detalhes da detecção facial.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
