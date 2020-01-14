---
title: 'Início rápido: reconhecer a fala de um C# microfone, (Unity)-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 149ec84732171cd57b3f8a9590d5340107536eac
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928105"
---
> [!NOTE]
> O SDK de fala para o Unity dá suporte à área de trabalho do Windows (x86 e x64) ou Plataforma Universal do Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (x64 Simulator, ARM32 e ARM64)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=unity)
> * Verifique se você tem acesso a um microfone para captura de áudio

Se você já fez isso, ótimo. Vamos continuar.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

1. Abra o Unity. Se você estiver usando o Unity pela primeira vez, a janela *<version number>* do **Hub do Unity** será exibida. (Você também pode abrir o Hub do Unity diretamente para chegar a essa janela.)

   [![janela do Hub do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecione **Novo**. A janela **criar um novo projeto com o Unity** *<version number>* é exibida.

   [![criar um novo projeto no Hub do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Em **nome do projeto**, digite **Csharp-Unity**.
1. Em **modelos**, se **3D** ainda não estiver selecionado, selecione-o.
1. Em **local**, selecione ou crie uma pasta na qual salvar o projeto.
1. Selecione **Criar**.

Após um pouco de tempo, a janela do editor do Unity é exibida.



## <a name="add-ui"></a>Adicionar interface do usuário

Agora, vamos adicionar uma interface do usuário mínima à nossa cena. Essa interface do usuário consiste em um botão para disparar o reconhecimento de fala e um campo de texto para exibir o resultado. Na janela [ **hierarquia** ](https://docs.unity3d.com/Manual/Hierarchy.html), uma cena de exemplo é mostrada como Unity criado com o novo projeto.

1. Na parte superior da janela **hierarquia** , selecione o **botão** **criar** > **interface do usuário** > .

   Essa ação cria três objetos de jogo que você pode ver na janela **hierarquia** : um objeto de **botão** , um objeto **Canvas** contendo o botão e um objeto **EventSystem** .

   [ambiente do editor do Unity ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navegue pela exibição de **cena** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que você tenha uma boa exibição da tela e do botão na [exibição de **cena** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Na janela [ **Inspetor** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita), defina as propriedades **pos X** e **pos Y** como **0**, para que o botão seja centralizado no meio da tela.

1. Na janela **hierarquia** , selecione **criar** > **interface do usuário** > **texto** para criar um objeto de **texto** .

1. Na janela **Inspetor** , defina as propriedades **pos X** e **pos Y** como **0** e **120**e defina as propriedades **Width** e **Height** como **240** e **120**. Esses valores garantem que o campo de texto e o botão não se sobreponham.

Quando terminar, a exibição de **cena** deverá ser semelhante a esta captura de tela:

[![exibição de cena no editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

Para adicionar o código de script de exemplo para o projeto do Unity, siga estas etapas:

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html), selecione **criar** >  **C# script** para adicionar um novo C# script.

   [![janela do projeto no editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nomeie o script `HelloWorld`.

1. Clique duas vezes em `HelloWorld` para editar o script recém-criado.

   > [!NOTE]
   > Para configurar o editor de código a ser usado pelo Unity para edição, selecione Editar **preferências**de > e, em seguida, vá para as preferências de **Ferramentas externas** . Para obter mais informações, consulte o [manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua o script existente pelo código a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no script.

Agora, retorne ao editor do Unity e adicione o script como um componente a um dos seus objetos de jogo:

1. Na janela **hierarquia** , selecione o objeto **Canvas** .

1. Na janela **Inspetor** , selecione o botão **Adicionar componente** .

   [janela Inspetor de ![no editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na lista suspensa, pesquise o script de `HelloWorld` que criamos acima e adicione-o. Uma seção **Olá, mundo (script)** aparece na janela **Inspetor** , listando duas propriedades não inicializadas, **texto de saída** e o **botão iniciar recup**. Essas propriedades do componente do Unity correspondem às propriedades públicas da classe `HelloWorld`.

1. Selecione o seletor de objetos da propriedade **botão iniciar recup** (o pequeno ícone de círculo à direita da propriedade) e escolha o objeto de **botão** que você criou anteriormente.

1. Selecione o seletor de objetos da propriedade **texto de saída** e escolha o objeto de **texto** que você criou anteriormente.

   > [!NOTE]
   > O botão também tem um objeto de texto aninhado. Verifique se você não o selecionou acidentalmente para saída de texto (ou renomeie um dos objetos de texto usando o campo **nome** na janela **Inspetor** para evitar confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no editor do Unity

Agora você está pronto para executar o aplicativo no editor do Unity.

1. Na barra de ferramentas do editor do Unity (abaixo da barra de menus), selecione o botão **reproduzir** (um triângulo apontando para a direita).

1. Vá para o modo de exibição de [ **jogo** ](https://docs.unity3d.com/Manual/GameView.html)e aguarde até que o objeto de **texto** seja exibido **clique no botão para reconhecer a fala**. (Ele exibe o **novo texto** quando o aplicativo não foi iniciado ou não está pronto para responder.)

1. Selecione o botão e fale uma frase ou sentença em inglês no microfone do seu computador. Sua fala é transmitida para o serviço de fala e transcrita para texto, que aparece na exibição do **jogo** .

   [![exibição de jogo no editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verifique a [janela do **console** ](https://docs.unity3d.com/Manual/Console.html) em busca de mensagens de depuração. Se a janela do **console** não estiver sendo exibida, vá para a barra de menus e selecione **janela** > **geral** > **console** para exibi-la.

1. Quando você terminar de reconhecer a fala, selecione o botão **reproduzir** na barra de ferramentas do editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar este aplicativo

Esse aplicativo também pode ser implantado como um aplicativo Android, um aplicativo autônomo do Windows ou um aplicativo UWP.
Para obter mais informações, consulte nosso [repositório de exemplo](https://aka.ms/csspeech/samples). A pasta `quickstart/csharp-unity` descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
