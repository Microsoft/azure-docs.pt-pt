---
title: 'Início rápido: Sintetizar fala, Java (Android) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em Java no Android usando o SDK de fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804042"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início rápido: Sintetizar a fala em Java no Android usando o SDK de fala

Os guias de início rápido também estão disponíveis para o [reconhecimento de fala](quickstart-java-android.md) e o [assistente virtual de voz primeiro](quickstart-virtual-assistant-java-android.md).

Neste artigo, você aprenderá a desenvolver um aplicativo Java para Android usando o SDK de fala dos serviços cognitivas para sintetizar a fala a partir do texto.
O aplicativo é baseado no pacote do Speech SDK Maven, na versão 1.7.0 e no Android Studio 3,3.
Atualmente, o SDK de Voz é compatível com dispositivos Android com processadores ARM de 32/64 bits ou Intel x86/x64.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar a interface de utilizador

Vamos criar uma interface de utilizador básica para a aplicação. Edite o esquema da atividade principal, `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do seu aplicativo e um TextView que contém o texto "Olá, Mundo!".

1. Clique no elemento TextView. Altere seu atributo de ID no canto superior direito para `outputMessage` e arraste-o para a tela inferior. Exclua seu texto.

1. Na paleta no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

1. Nos atributos do botão à direita, no valor para o atributo `onClick`, introduza `onSpeechButtonClicked`. Vamos escrever um método com este nome para processar o evento do botão.  Altere o atributo ID no canto superior direito para `button`.

1. Arraste um texto sem formatação para o espaço acima do botão; altere seu atributo de ID para `speakText` e altere o atributo Text para `Hi there!`.

1. Utilize o ícone de varinha mágica na parte superior do designer para inferir restrições de esquema.


    ![Captura de ecrã do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ter esta aparência:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem `MainActivity.java`. Substitua todo o código neste ficheiro pelo seguinte.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onSpeechButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um pressionamento de botão dispara A síntese de fala.

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo. Como alternativa, crie um [emulador do Android](https://developer.android.com/studio/run/emulator).

1. Para criar a aplicação, prima Ctrl+F9 ou escolha **Build** (Criar)  > **Make Project** (Criar Projeto) na barra de menus.

1. Para iniciar a aplicação, prima Shift+F10 ou escolha **Run** (Executar)  > **Run 'app'** (Executar 'app').

1. Na janela destino de implantação exibida, escolha seu dispositivo Android ou emulador.

   ![Captura de ecrã da janela de seleção de destino de implementação](media/sdk/qs-java-android-12-deploy.png)

Insira um texto e pressione o botão no aplicativo para iniciar uma seção de síntese de fala. Você ouvirá o áudio sintetizado do alto-falante padrão e verá as informações de `speech synthesis succeeded` na tela.

![Captura de ecrã da aplicação Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)
