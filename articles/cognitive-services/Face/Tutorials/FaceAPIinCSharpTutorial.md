---
title: 'Tutorial: Detetar e exibir dados de rostos numa imagem com o SDK .NET'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação do Windows que utiliza a API de rostos para detetar e quadro rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: 6a60afc45894518f92115976876ddd50efa1e410
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815331"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Tutorial: Criar um aplicativo do WPF para exibir dados de rostos numa imagem

Neste tutorial, irá aprender como utilizar a API Face do Azure, através do cliente de .NET SDK, para detetar rostos numa imagem e, em seguida, apresentar esses dados na IU. Irá criar um aplicativo simples do Windows Presentation Framework (WPF) que Deteta rostos, desenha um quadro em torno de cada rosto e apresenta uma descrição do mostrador na barra de status. 

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação WPF
> - Instalar a biblioteca de cliente de API de rostos
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado
> - Exibir uma descrição do mostrador da realçado na barra de status

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)

O código de exemplo completo está disponível na [exemplo cognitivos Face CSharp](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) repositório no GitHub.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 


## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Siga estes passos para criar um novo projeto de aplicativo do WPF.

1. No Visual Studio, abra a caixa de diálogo novo projeto. Expanda **instalada**, em seguida, **Visual C#** , em seguida, selecione **WPF App (.NET Framework)**.
1. Dê à aplicação o nome **FaceTutorial** e, em seguida, clique em **OK**.
1. Obtenha os pacotes NuGet necessários. Com o botão direito no seu projeto no Solution Explorer e selecione **gerir pacotes NuGet**; em seguida, localizar e instalar o pacote seguinte:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Adicionar o código inicial

Nesta secção, irá adicionar a estrutura básica da aplicação sem seus recursos específicos face.

### <a name="create-the-ui"></a>Criar a interface do Usuário

Open *mainwindow. XAML* e substitua os conteúdos com o código a seguir&mdash;esta ação cria a janela da interface do Usuário. Tenha em atenção que `FacePhoto_MouseMove` e `BrowseButton_Click` são manipuladores de eventos que definirá posteriormente.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Criar a classe principal

Open *MainWindow.xaml.cs* e adicionar espaços de nomes da biblioteca de cliente, juntamente com outros espaços de nomes necessários. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Em seguida, insira o seguinte código no **MainWindow** classe. Esta ação cria um **FaceClient** instância com a chave de subscrição, que tem de introduzir por conta própria. Também tem de definir a cadeia de caracteres de região `faceEndpoint` para a região correta para a sua subscrição (consulte a [documentos da API de rostos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todos os pontos finais de região).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Em seguida, cole o código seguinte para o **MainWindow** método.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Por fim, adicione a **BrowseButton_Click** e **FacePhoto_MouseMove** métodos à classe. Elas correspondem aos manipuladores de eventos declarados na *mainwindow. XAML*. O **BrowseButton_Click** método cria uma **OpenFileDialog**, que permite que o usuário selecionar uma imagem. jpg. Em seguida, ele exibe a imagem na janela principal. Irá inserir o código restante **BrowseButton_Click** e **FacePhoto_MouseMove** em passos posteriores. Tenha também em atenção a `faceList` referência&mdash;uma lista de **DetectedFace** objetos. Isso é onde a aplicação irá armazenar e chamar os dados de rosto real.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Experimentar a aplicação

Prima **Iniciar** no menu para testar a sua aplicação. Quando abre a janela da aplicação, clique em **procurar** no canto inferior esquerdo. R **File Open** deverá aparecer a caixa de diálogo. Selecionar uma imagem de seu sistema de ficheiros e certifique-se de que ele exibe na janela. Em seguida, feche a aplicação e avance para o passo seguinte.

![Captura de ecrã que mostra uma imagem não modificada de rostos](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Carregar a imagem e detetar rostos

A aplicação irá detetar rostos ao chamar o **FaceClient.Face.DetectWithStreamAsync** método, que encapsula o [detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API REST para carregar uma imagem local.

Inserir o seguinte método na **MainWindow** classe, veja a seguir os **FacePhoto_MouseMove** método. Isso define uma lista de atributos faciais para recuperar e lê o ficheiro de imagem submetidas para uma **Stream**. Em seguida, ele passa para ambos os **DetectWithStreamAsync** chamada de método.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Desenhar retângulos em torno de rostos

Em seguida, irá adicionar o código para desenhar um retângulo em torno de cada detetado rosto na imagem. Na **MainWindow** de classe, insira o seguinte código no final da **BrowseButton_Click** método, após o `FacePhoto.Source = bitmapSource` linha. Isto preenche uma lista de rostos detetados da chamada para **UploadAndDetectFaces**. Em seguida, ele desenha um retângulo em torno de cada rosto e exibe pela imagem modificada na janela principal.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Descreva os rostos

Adicione o seguinte método para o **MainWindow** classe, veja a seguir os **UploadAndDetectFaces** método. Desta forma, os atributos de face obtidos numa cadeia de caracteres que descreve a aparência.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Apresentar a descrição de rostos

Adicione o seguinte código para o **FacePhoto_MouseMove** método. Esse manipulador de eventos apresenta a cadeia de descrição de rostos em `faceDescriptionStatusBar` quando o cursor for parado sobre um retângulo de rostos detetados.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem que tenha um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Deverá ver um retângulo vermelho em cada um dos rostos na imagem. Se mover o mouse sobre um retângulo de rostos, a descrição do que enfrentam deve aparecer na barra de status.

![Captura de ecrã que mostra os rostos detetados, enquadrados com retângulos](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber o processo básico para utilizar o SDK de .NET do serviço de rostos e criou uma aplicação para detetar e quadro rostos numa imagem. Em seguida, saiba mais sobre os detalhes da deteção de rostos.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
