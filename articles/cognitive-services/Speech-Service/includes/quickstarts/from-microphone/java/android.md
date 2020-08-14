---
title: 'Quickstart: Reconhecer o discurso a partir de um microfone, Java (Android) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer o discurso em Java no Android usando o Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 5832920ec491ce9d99219a7a423fcf0e38ba62cc
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226141"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azul](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="create-a-user-interface"></a>Criar uma interface de utilizador

Agora vamos criar uma interface básica de utilizador para a aplicação. Edite o esquema da atividade principal, `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome da sua aplicação, e uma TextView que contém o texto "Hello World!".

* Selecione o elemento TextView. Altere o atributo ID no canto superior direito para `hello`.

* A partir da paleta no canto superior esquerdo da `activity_main.xml` janela, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, introduza `onSpeechButtonClicked`. Vamos escrever um método com este nome para processar o evento do botão. Altere o atributo ID no canto superior direito para `button`.

* Utilize o ícone de varinha mágica na parte superior do designer para inferir restrições de esquema.

  ![Captura de ecrã do ícone de varinha mágica](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua UI devem agora ser assim:

![Interface de utilizador](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem `MainActivity.java`. Substitua todo o código deste ficheiro pelo seguinte:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita as permissões de microfone e internet, e inicializa o enlace de plataforma nativa. A configuração dos enlaces de plataforma nativa só é precisa uma vez. Deve ser feita no início durante a inicialização da aplicação.

   * O método `onSpeechButtonClicked` é, como observado anteriormente, o processador do clique do botão. Uma pressão de botão aciona a transcrição de fala para texto.

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia `YourServiceRegion` pelo identificador da **Região** da [região](https://aka.ms/speech/sdkregion) associada à sua subscrição.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou [o modo de desenvolvimento e a depuragem USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para construir a aplicação, selecione Ctrl+F9 ou selecione **Build**  >  **Make Project** a partir da barra de menu.

1. Para lançar a aplicação, selecione Shift+F10 ou selecione **'app' Run**  >  **Run.**

1. Na janela alvo de implementação que aparece, selecione o seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Selecione o botão da aplicação para iniciar uma secção de reconhecimento de voz. Os próximos 15 segundos de voz em inglês serão enviados para o serviço de Voz e transcritos. O resultado é apresentado na aplicação Android e na janela logcat no Android Studio.

![Captura de ecrã da aplicação Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
