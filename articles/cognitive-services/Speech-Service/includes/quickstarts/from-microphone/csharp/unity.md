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
ms.openlocfilehash: 894c9d34f3e9874a51f5939021f908ac4ecaaaa9
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156361"
---
> [!NOTE]
> O Speech SDK for Unitity suporta windows desktop (x86 e x64) ou Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulador x64, ARM32 e ARM64)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=unity)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

Se já fez isto, ótimo. Vamos continuar.

## <a name="create-a-unity-project"></a>Criar um projeto de Unidade

1. Unidade Aberta. Se estás a usar a Unidade pela primeira vez, aparece o **Centro de Unidade** *<version number>* janela. (Você também pode abrir o Unity Hub diretamente para chegar a esta janela.)

   [janela do Hub da Unidade ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecione **Novo**. Surge um novo projeto com a **Janela<version number>unidade.**

   [![Criar um novo projeto no Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Em **Nome do Projeto,** entre em **csharp-unidade.**
1. Em **Modelos**, se **o 3D** ainda não estiver selecionado, selecione-o.
1. No **Local**, selecione ou crie uma pasta para salvar o projeto.
1. Selecione **Criar**.

Depois de um pouco de tempo, a janela do Editor da Unidade aparece.



## <a name="add-ui"></a>Adicionar UI

Agora vamos adicionar um Mínimo de UI à nossa cena. Esta UI consiste num botão para desencadear o reconhecimento da fala e um campo de texto para exibir o resultado. Na [janela **da Hierarquia,** ](https://docs.unity3d.com/Manual/Hierarchy.html)mostra-se uma amostra que a Unidade criou com o novo projeto.

1. No topo da janela **da Hierarquia,** selecione **Criar** > **Botão** > **UI** .

   Esta ação cria três objetos de jogo que podes ver na janela **da Hierarquia:** um objeto **botão,** um objeto **de lona** contendo o botão e um objeto **EventSystem.**

   [ambiente editor de unidade ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navegue na vista **cena** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que tenha uma boa vista da tela e do botão na vista [ **cena** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Na [janela **do Inspetor** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por defeito à direita), coloque as propriedades Pos **X** e **Pos Y** para **0,** de modo que o botão está centrado no meio da tela.

1. Na janela **da Hierarquia,** selecione **Criar** > **UI** > **Texto** para criar um objeto **de texto.**

1. Na janela **do Inspetor,** coloque as propriedades **Pos X** e **Pos Y** para **0** e **120,** e coloque as propriedades **de Largura** e **Altura** para **240** e **120**. Estes valores asseguram que o campo de texto e o botão não se sobrepõem.

Quando terminar, a vista **cena** deve parecer semelhante a esta imagem:

[![vista de cena no Editor da Unidade](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

Para adicionar o código de script da amostra para o projeto Unidade, siga estes passos:

1. Na [janela do Projeto](https://docs.unity3d.com/Manual/ProjectView.html), selecione C# **Criar** >  **C# script** para adicionar um novo script.

   [janela do projeto ![no Editor da Unidade](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Diga o nome do guião `HelloWorld`.

1. Clique duas vezes `HelloWorld` para editar o script recém-criado.

   > [!NOTE]
   > Para configurar o editor de código a ser usado pela Unidade para edição, **selecione Editar** **preferências** > , e depois ir às preferências das **Ferramentas Externas.** Para mais informações, consulte o Manual do [Utilizador da Unidade](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua o script existente pelo seguinte código:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Encontre e substitua a `YourSubscriptionKey` de cadeia seleção com a sua chave de subscrição do serviço Speech.

1. Encontre e substitua a cadeia `YourServiceRegion` pelo "Parâmetro SDK de Fala" da [região](https://aka.ms/speech/sdkregion) associado à sua subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações no guião.

Agora volte ao Editor de Unidade e adicione o script como componente a um dos seus objetos de jogo:

1. Na janela **da Hierarquia, selecione** o objeto **de lona.**

1. Na janela **do Inspetor,** selecione o botão **Adicionar Componente.**

   [janela do Inspetor ![no Editor da Unidade](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na lista de lançamentos, procure o `HelloWorld` script que criamos acima e adicione-o. Uma secção **Hello World (Script)** aparece na janela do **Inspetor,** listando duas propriedades não inicializadas, **Texto de Saída** e **Botão Start Reco**. Estas propriedades componentes de Unidade combinam com as propriedades públicas da classe `HelloWorld`.

1. Selecione o picker de objetos da propriedade **Start Reco Button** (o pequeno ícone do círculo à direita da propriedade) e escolha o objeto **botão** que criou anteriormente.

1. Selecione o apanhador de objetos da propriedade **De texto** de saída e escolha o objeto **de Texto** que criou anteriormente.

   > [!NOTE]
   > O botão também tem um objeto de texto aninhado. Certifique-se de que não o escolhe acidentalmente para saída de texto (ou mude o nome de um dos objetos de texto utilizando o campo **Nome** na janela do **Inspetor** para evitar confusões).

## <a name="run-the-application-in-the-unity-editor"></a>Executar a aplicação no Editor de Unidade

Agora está pronto para executar a candidatura dentro do Editor da Unidade.

1. Na barra de ferramentas Do Editor de Unidade (abaixo da barra de menus), selecione o botão **Reproduzir** (um triângulo de apontar à direita).

1. Vá à vista do [ **Jogo** ](https://docs.unity3d.com/Manual/GameView.html), e aguarde que o objeto **de texto** apresente **o botão Click para reconhecer a fala**. (Exibe **Novo Texto** quando a aplicação ainda não começou ou não está pronta para responder.)

1. Selecione o botão e diga uma frase ou frase em inglês no microfone do computador. O seu discurso é transmitido ao serviço da Fala e transcrito para texto, que aparece na vista do **Jogo.**

   [![vista do jogo no Editor da Unidade](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verifique a janela da [ **Consola** ](https://docs.unity3d.com/Manual/Console.html) para obter mensagens de depuração. Se a janela da **Consola** não estiver a aparecer, vá à barra de menus e selecione **Janela** > **Consola geral** ** > ** para exibi-la.

1. Quando terminar de reconhecer o discurso, selecione o botão **Reproduzir** na barra de ferramentas Do Editor de Unidade para parar a aplicação.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar esta aplicação

Esta aplicação também pode ser implementada como uma aplicação Android, uma aplicação autónoma do Windows ou uma aplicação UWP.
Para mais informações, consulte o nosso [repositório de amostras.](https://aka.ms/csspeech/samples) A pasta `quickstart/csharp-unity` descreve a configuração para estes alvos adicionais.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
