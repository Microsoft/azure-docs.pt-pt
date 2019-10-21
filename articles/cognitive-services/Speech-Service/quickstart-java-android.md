---
title: 'Início rápido: reconhecer fala, Java (Android)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala em Java no Android usando o SDK de fala
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675593"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: reconhecer voz em Java no Android com o SDK de Voz

Os guias de início rápido também estão disponíveis para [síntese de fala](quickstart-text-to-speech-java-android.md) e [assistente virtual de voz primeiro](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprende a desenvolver um aplicativo Java para Android usando o SDK de fala dos serviços cognitivas do Azure para transcrever a fala para o texto.

O aplicativo é baseado no pacote do Speech SDK Maven e Android Studio 3,3. O SDK de fala atualmente é compatível com dispositivos Android que têm processadores de 32 bits ou 64 de ARM e Intel x86/x64 compatíveis.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Para obter mais informações, consulte [experimentar os serviços de fala gratuitamente](get-started.md).

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Criar uma interface do usuário

Agora, vamos criar uma interface do usuário básica para o aplicativo. Edite o esquema da atividade principal, `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do seu aplicativo e um TextView que contém o texto "Olá, Mundo!".

* Selecione o elemento TextView. Altere o atributo ID no canto superior direito para `hello`.

* Na paleta no canto superior esquerdo da janela de `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, introduza `onSpeechButtonClicked`. Vamos escrever um método com este nome para processar o evento do botão. Altere o atributo ID no canto superior direito para `button`.

* Utilize o ícone de varinha mágica na parte superior do designer para inferir restrições de esquema.

  ![Captura de ecrã do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ter esta aparência:

![Interface de utilizador](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem `MainActivity.java`. Substitua todo o código deste arquivo pelo seguinte:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita as permissões de microfone e internet, e inicializa o enlace de plataforma nativa. A configuração dos enlaces de plataforma nativa só é precisa uma vez. Deve ser feita no início durante a inicialização da aplicação.

   * O método `onSpeechButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um pressionamento de botão dispara a transcrição de fala em texto.

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que você habilitou o [modo de desenvolvimento e a depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para criar o aplicativo, selecione CTRL + F9 ou selecione **compilar**  > **criar projeto** na barra de menus.

1. Para iniciar o aplicativo, selecione Shift + F10 ou selecione **executar**  > **Executar ' aplicativo '** .

1. Na janela destino de implantação exibida, selecione seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](media/sdk/qs-java-android-12-deploy.png)

Selecione o botão no aplicativo para iniciar uma seção de reconhecimento de fala. Os próximos 15 segundos de fala em inglês serão enviados aos serviços de fala e transcritas. O resultado é apresentado na aplicação Android e na janela logcat no Android Studio.

![Captura de ecrã da aplicação Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Ver também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
