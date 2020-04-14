---
title: 'Quickstart: Discurso sintetizado, Java (Android) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar o discurso em Java no Android usando o Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: d114e75a08f31a664772b84e19ec4d93b453af0b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275198"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>Criar a interface de utilizador

Vamos criar uma interface de utilizador básica para a aplicação. Edite o esquema da atividade principal, `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome da sua aplicação, e um TextView contendo o texto "Hello World!".

1. Clique no elemento TextView. Mude o seu atributo de ID `outputMessage`no canto superior direito para , e arraste-o para o ecrã inferior. Apagar o seu texto.

1. Na paleta no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

1. Nos atributos do botão à direita, no valor para o atributo `onClick`, introduza `onSpeechButtonClicked`. Vamos escrever um método com este nome para processar o evento do botão.  Altere o atributo ID no canto superior direito para `button`.

1. Arraste um Texto Simples para o espaço acima do botão; alterar o seu `speakText`atributo de IDENTIFICAção `Hi there!`para , e alterar o atributo de texto para .

1. Utilize o ícone de varinha mágica na parte superior do designer para inferir restrições de esquema.


    ![Captura de ecrã do ícone de varinha mágica](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua UI devem agora ser assim:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem `MainActivity.java`. Substitua todo o código neste ficheiro pelo seguinte.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onSpeechButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um botão de pressão aciona a síntese da fala.

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo. Alternativamente, crie um [emulador Android.](https://developer.android.com/studio/run/emulator)

1. Para construir a aplicação, prima Ctrl+F9 ou escolha **Build** > **Make Project** a partir da barra de menus.

1. Para lançar a aplicação, prima Shift+F10 ou escolha > **'App'** **Run**Run .

1. Na janela-alvo de implementação que aparece, escolha o seu dispositivo Android ou emulador.

   ![Captura de ecrã da janela de seleção de destino de implementação](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Introduza um texto e pressione o botão na aplicação para iniciar uma secção de síntese de fala. Ouvirá o áudio sintetizado do altifalante `speech synthesis succeeded` predefinido e verá a informação no ecrã.

![Captura de ecrã da aplicação Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
