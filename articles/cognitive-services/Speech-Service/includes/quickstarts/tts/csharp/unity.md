---
title: 'Início rápido: sintetizar fala C# , (Unity)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de conversão de texto em fala com o Unity e o SDK de fala para o Unity. Quando terminar, você poderá sintetizar a fala de texto em tempo real para o palestrante do dispositivo.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 281fbcf3f42160b18adcad3f06f6ef4a7b8de243
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502927"
---
> [!NOTE]
> O Unity dá suporte à área de trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (x64 Simulator, ARM32 e ARM64).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=unity)

## <a name="add-a-ui"></a>Adicionar uma interface do usuário

Adicionamos uma interface do usuário mínima à nossa cena que consiste em um campo de entrada para inserir o texto de síntese, um botão para disparar a síntese de fala e um campo de texto para exibir o resultado.

* Na [janela hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por padrão à esquerda), uma cena de exemplo é mostrada como Unity criado com o novo projeto.
* Selecione o botão **criar** na parte superior da janela **hierarquia** e selecione a **interface do usuário** > campo de **entrada**.
* Essa opção cria três objetos de jogo que você pode ver na janela **hierarquia** : um objeto de **campo de entrada** aninhado em um objeto **Canvas** e um objeto **EventSystem** .
* [Navegue pelo modo de exibição de cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para que você tenha uma boa exibição da tela e do campo de entrada na [exibição de cena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecione o objeto **campo de entrada** na janela **hierarquia** para exibir suas configurações na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
* Defina as propriedades **pos X** e **pos Y** como **0** para que o campo de entrada seja centralizado no meio da tela.
* Selecione o botão **criar** na parte superior da janela **hierarquia** novamente. Selecione o **botão** > **da interface do usuário** para criar um botão.
* Selecione o objeto de **botão** na janela **hierarquia** para exibir suas configurações na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
* Defina as propriedades **pos X** e **pos Y** como **0** e **-48**. Defina as propriedades **Width** e **Height** como **160** e **30** para garantir que o botão e o campo de entrada não se sobreponham.
* Selecione o botão **criar** na parte superior da janela **hierarquia** novamente. Selecione a **interface do usuário** > **texto** para criar um campo de texto.
* Selecione o objeto de **texto** na janela **hierarquia** para exibir suas configurações na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
* Defina as propriedades **pos X** e **pos Y** como **0** e **80**. Defina as propriedades **Width** e **Height** como **320** e **80** para garantir que o campo de texto e o campo de entrada não se sobreponham.
* Selecione o botão **criar** na parte superior da janela **hierarquia** novamente. Selecione **áudio** > **fonte de áudio** para criar uma fonte de áudio.

Quando tiver terminado, a interface do usuário deverá ser semelhante a esta captura de tela:

[![captura de tela da interface do usuário de início rápido no editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html) (por padrão na parte inferior esquerda), selecione o botão **criar** e, em seguida, selecione  **C# script**. Nomeie o script `HelloWorld`.

1. Edite o script clicando duas vezes nele.

   > [!NOTE]
   > Você pode configurar qual editor de código é iniciado selecionando **Editar** **preferências**de > . Para obter mais informações, consulte o [manual do usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código pelo seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura dos serviços de fala.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição. Por exemplo, a região será `westus` se você usar a avaliação gratuita.

1. Salve as alterações no script.

1. De volta ao editor do Unity, adicione o script como um componente a um dos seus objetos de jogo.

   * Selecione o objeto **Canvas** na janela **hierarquia** para abrir a configuração na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão à direita).
   * Selecione o botão **Adicionar componente** na janela **Inspetor** . Em seguida, pesquise o script de `HelloWorld` que criamos anteriormente e o adicionei.
   * O componente HelloWorld tem quatro propriedades não inicializadas, **texto de saída**, **campo de entrada**, botão de **fala** e fonte de **áudio**, que correspondem às propriedades públicas da classe `HelloWorld`.
     Para conectá-los, selecione o seletor de objetos (o ícone de círculo pequeno à direita da propriedade). Selecione o texto e os objetos de botão que você criou anteriormente.

     > [!NOTE]
     > O campo de entrada e o botão também têm um objeto de texto aninhado. Verifique se você não o selecionou acidentalmente para saída de texto. Ou, você pode renomear os objetos de texto que usam o campo **nome** na janela **Inspetor** para evitar essa confusão.

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no editor do Unity

* Selecione o botão **reproduzir** na barra de ferramentas do editor do Unity abaixo da barra de menus.
* Depois que o aplicativo for iniciado, insira algum texto no campo de entrada e selecione o botão. Seu texto é transmitido para o serviço de fala e sintetizado para fala, que é reproduzido no seu palestrante.

  [![captura de tela do início rápido em execução na janela de jogos do Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Verifique a [janela do console](https://docs.unity3d.com/Manual/Console.html) em busca de mensagens de depuração.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar este aplicativo

Esse aplicativo também pode ser implantado no Android como um aplicativo autônomo do Windows ou um aplicativo UWP.
Consulte o [repositório de exemplo](https://aka.ms/csspeech/samples) na pasta QuickStart/Csharp-Unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consultar também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
