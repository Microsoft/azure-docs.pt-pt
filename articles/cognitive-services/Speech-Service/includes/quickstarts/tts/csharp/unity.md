---
title: 'Quickstart: Discurso sintetizador, C# (Unidade) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de texto-a-fala com a Unidade e o SDK de Discurso para a Unidade. Quando terminar, pode sintetizar o discurso do texto em tempo real para o altifalante do seu dispositivo.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274862"
---
> [!NOTE]
> A Unidade suporta o Windows Desktop (x86 e x64) ou a Plataforma Universal windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulador x64, ARM32 e ARM64).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Adicione um UI

Adicionamos um UI mínimo à nossa cena que consiste num campo de entrada para introduzir o texto para síntese, um botão para desencadear a síntese da fala e um campo de texto para exibir o resultado.

* Na [janela da Hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por defeito à esquerda), mostra-se uma cena de amostra que a Unidade criou com o novo projeto.
* Selecione o botão **Criar** na parte superior da janela **da Hierarquia** e selecione **UI** > **Input Field**.
* Esta opção cria três objetos de jogo que podes ver na janela **da Hierarquia:** um objeto de Campo de **Entrada** aninhado dentro de um objeto **de Lona** e um objeto **EventSystem.**
* [Navegue na vista cena para](https://docs.unity3d.com/Manual/SceneViewNavigation.html) que tenha uma boa vista da tela e do campo de entrada na vista [Cena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecione o objeto **de Campo** de Entrada na janela **da Hierarquia** para exibir as suas definições na janela [do Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por defeito à direita).
* Desloque as propriedades **pos X** e **Pos Y** para **0** de modo que o campo de entrada esteja centrado no meio da tela.
* Selecione novamente o botão **Criar** na parte superior da janela **da Hierarquia.** Selecione**botão** **UI** > para criar um botão.
* Selecione o objeto **botão** na janela **da Hierarquia** para exibir as suas definições na janela [do Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por defeito à direita).
* Desloque as propriedades **pos X** e **Pos Y** para **0** e **-48**. Defina as propriedades **width** e **altura** para **160** e **30** para garantir que o botão e o campo de entrada não se sobreponham.
* Selecione novamente o botão **Criar** na parte superior da janela **da Hierarquia.** Selecione**Texto** **UI** > para criar um campo de texto.
* Selecione o objeto **texto** na janela **da Hierarquia** para exibir as suas definições na [janela do Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por defeito à direita).
* Desloque as propriedades **Pos X** e **Pos Y** para **0** e **80**. Defina as propriedades **width** e **altura** para **320** e **80** para garantir que o campo de texto e o campo de entrada não se sobreponham.
* Selecione novamente o botão **Criar** na parte superior da janela **da Hierarquia.** Selecione **Audio Audio** > **Source** para criar uma fonte de áudio.

Quando terminar, a UI deve parecer semelhante a esta imagem:

[![Screenshot da interface de utilizador quickstart no Editor de Unidade](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Na [janela Do Projeto](https://docs.unity3d.com/Manual/ProjectView.html) (por predefinição na parte inferior esquerda), selecione o botão **Criar** e, em seguida, selecione o **script C#**. Diga o `HelloWorld`nome do guião.

1. Editar o script clicando duas vezes.

   > [!NOTE]
   > Pode configurar qual o editor de código lançado selecionando**Preferências**de **Edição** > . Para mais informações, consulte o Manual do [Utilizador da Unidade](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código pelo seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e `YourSubscriptionKey` substitua a cadeia pela chave de subscrição do serviço Speech.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição. Por exemplo, a `westus` região é se você usar o teste gratuito.

1. Guarde as alterações no guião.

1. De volta ao Editor de Unidade, adicione o script como um componente a um dos seus objetos de jogo.

   * Selecione o objeto **de lona** na janela **da Hierarquia** para abrir a definição na janela [do Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por defeito à direita).
   * Selecione o botão **Adicionar Componente** na janela **do Inspetor.** Em seguida, `HelloWorld` procure o script que criamos anteriormente e adicione-o.
   * O componente HelloWorld tem quatro propriedades não inicializadas, **Texto de Saída,** Campo de **Entrada,** **Botão de Fala** e Fonte de **Áudio,** que combinam com as propriedades públicas da `HelloWorld` classe.
     Para os ligar, selecione o Object Picker (o ícone do pequeno círculo à direita da propriedade). Selecione o texto e os objetos de botão que criou anteriormente.

     > [!NOTE]
     > O campo de entrada e o botão também têm um objeto de texto aninhado. Certifique-se de que não o escolhe acidentalmente para a saída de texto. Ou, pode renomear os objetos de texto que usam o campo **Nome** na janela do **Inspetor** para evitar essa confusão.

## <a name="run-the-application-in-the-unity-editor"></a>Executar a aplicação no Editor de Unidade

* Selecione o botão **Reproduzir** na barra de ferramentas Do Editor de Unidade que está por baixo da barra de menus.
* Depois do lançamento da aplicação, introduza algum texto no campo de entrada e selecione o botão. O seu texto é transmitido ao serviço da Fala e sintetizado para a fala, que reproduz no seu orador.

  [![Screenshot do arranque rápido na janela do Jogo da Unidade](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Verifique a janela da [Consola](https://docs.unity3d.com/Manual/Console.html) para obter mensagens de depuração.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para executar esta aplicação

Esta aplicação também pode ser implementada para Android como uma aplicação autónoma do Windows ou uma aplicação UWP.
Consulte o [repositório](https://aka.ms/csspeech/samples) de amostras na pasta quickstart/csharp-unitity que descreve a configuração para estes alvos adicionais.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
