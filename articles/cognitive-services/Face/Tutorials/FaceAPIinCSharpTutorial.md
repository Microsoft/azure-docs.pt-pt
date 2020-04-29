---
title: 'Tutorial: Detetar e exibir dados faciais numa imagem utilizando o .NET SDK'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação Windows que utiliza o serviço Face para detetar e enquadrar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: b4458920ec8b3e0c302f6e0654891b83ed07264f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402900"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Tutorial: Criar uma aplicação de quadro de apresentação do Windows (WPF) para exibir dados faciais numa imagem

Neste tutorial, você vai aprender a usar o serviço Azure Face, através do sdK cliente .NET, para detetar rostos numa imagem e, em seguida, apresentar esses dados na UI. Você vai criar uma aplicação WPF que deteta rostos, desenha uma moldura em torno de cada rosto, e exibe uma descrição do rosto na barra de estado. 

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação WPF
> - Instale a biblioteca do cliente Face
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado
> - Mostrar uma descrição do rosto realçado na barra de estado

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)

O código completo da amostra está disponível no repositório de [amostras Cognitive Face CSharp](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) no GitHub.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 


## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de subscrição Face. Você pode obter uma chave de subscrição de teste gratuito da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço Face e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT`chave e corda final de serviço, nomeada e, respectivamente.
- Qualquer edição do [Visual Studio 2015 ou 2017.](https://www.visualstudio.com/downloads/)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Siga estes passos para criar um novo projeto de aplicação WPF.

1. No Estúdio Visual, abra o diálogo do Novo Projeto. Expandir **Instalado,** depois **Visual C#** e, em seguida, selecionar **A App WPF (.QUADRO NET)**.
1. Dê à aplicação o nome **FaceTutorial** e, em seguida, clique em **OK**.
1. Obtenha os pacotes NuGet necessários. Clique no seu projeto no Solution Explorer e selecione **Gerir pacotes NuGet;** em seguida, encontrar e instalar o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>Adicionar o código inicial

Nesta secção, você irá adicionar o quadro básico da app sem as suas funcionalidades específicas do rosto.

### <a name="create-the-ui"></a>Criar a UI

Abra *mainWindow.xaml* e substitua o&mdash;conteúdo pelo seguinte código que este código cria a janela UI. Os `FacePhoto_MouseMove` `BrowseButton_Click` métodos e métodos são manipuladores de eventos que definirá mais tarde.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Criar a classe principal

Abra *MainWindow.xaml.cs* e adicione os espaços de nome da biblioteca do cliente, juntamente com outros espaços de nome necessários. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Em seguida, insira o seguinte código na classe **MainWindow.** Este código cria uma instância **FaceClient** utilizando a chave de subscrição e o ponto final.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Em seguida, adicione o construtor **MainWindow.** Verifica a cadeia de URL do ponto final e, em seguida, associa-a ao objeto do cliente.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Finalmente, adicione os métodos **BrowseButton_Click** e **FacePhoto_MouseMove** à classe. Estes métodos correspondem aos manipuladores de eventos declarados em *MainWindow.xaml*. O método **BrowseButton_Click** cria um **OpenFileDialog,** que permite ao utilizador selecionar uma imagem .jpg. Em seguida, exibe a imagem na janela principal. Introduzirá o código restante para **BrowseButton_Click** e **FacePhoto_MouseMove** em etapas posteriores. Note também `faceList` &mdash;a referência a uma lista de objetos **DetectedFace.** Esta referência é onde a sua aplicação irá armazenar e ligar para os dados faciais reais.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Experimente a aplicação

Prima **Iniciar** no menu para testar a sua aplicação. Quando a janela da aplicação abrir, clique em **Navegar** no canto inferior esquerdo. Deve aparecer um diálogo aberto de **arquivo.** Selecione uma imagem do seu sistema de ficheiros e verifique se aparece na janela. Em seguida, feche a app e avance para o próximo passo.

![Captura de ecrã que mostra uma imagem não modificada de rostos](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detetar rostos

A sua aplicação irá detetar rostos através do método **FaceClient.Face.DetectWithStreamAsync,** que envolve a API de [Deteção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) DO REST para o upload de uma imagem local.

Insira o seguinte método na classe **MainWindow,** abaixo do método **FacePhoto_MouseMove.** Este método define uma lista de atributos faciais para recuperar e lê o ficheiro de imagem submetido num **Stream**. Em seguida, passa ambos estes objetos para a chamada do método **DetectWithStreamAsync.**

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Desenhar retângulos em torno de rostos

Em seguida, irá adicionar o código para desenhar um retângulo em torno de cada rosto detetado na imagem. Na classe **MainWindow,** insira o seguinte código no `FacePhoto.Source = bitmapSource` final do método **BrowseButton_Click,** após a linha. Este código preenche uma lista de rostos detetados da chamada para **UploadAndDetectFaces**. Em seguida, desenha um retângulo em torno de cada rosto e exibe a imagem modificada na janela principal.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Descreva os rostos

Adicione o seguinte método à classe **MainWindow,** abaixo do método **UploadAndDetectFaces.** Este método converte os atributos do rosto recuperado numa corda descrevendo o rosto.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Apresentar a descrição de rostos

Adicione o seguinte código ao método **FacePhoto_MouseMove.** Este manipulador de eventos `faceDescriptionStatusBar` exibe a cadeia de descrição facial quando o cursor paira sobre um retângulo facial detetado.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem que tenha um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Devia ver um retângulo vermelho em cada um dos rostos na imagem. Se mover o rato sobre um retângulo facial, a descrição desse rosto deve aparecer na barra de estado.

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu o processo básico de utilização do serviço Face .NET SDK e criou uma aplicação para detetar e enquadrar rostos numa imagem. Em seguida, saiba mais sobre os detalhes da deteção facial.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
