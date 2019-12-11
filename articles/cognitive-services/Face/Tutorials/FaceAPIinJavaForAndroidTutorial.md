---
title: 'Tutorial: Detetar e enquadrar rostos numa imagem com o SDK Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Android simples que usa o API de Detecção Facial para detectar e emoldurar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce0b308077505d5af1d757f1684c50505b11831e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977799"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Tutorial: criar uma aplicação Android para detetar e enquadrar rostos numa imagem

Neste tutorial, você criará um aplicativo Android que usa o API de Detecção Facial do Azure, por meio do SDK do Java, para detectar faces humanas em uma imagem. O aplicativo exibe uma imagem selecionada e desenha um quadro em torno de cada rosto detetado.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação Android
> - Instalar a biblioteca de cliente do API de Detecção Facial
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado

![Captura de ecrã do Android de uma fotografia com rostos enquadrados por um retângulo vermelho](../Images/android_getstarted2.1.PNG)

O código de exemplo completo está disponível no repositório do [Android de serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-face-android-sample) no github.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a cadeia de caracteres de ponto de extremidade de serviço e chave, denominada `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) com o nível de API 22 ou posterior (exigido pela biblioteca de cliente facial).

## <a name="create-the-android-studio-project"></a>Criar o projeto de Android Studio

Siga estas etapas para criar um novo projeto de aplicativo Android.

1. Em Android Studio, selecione **Iniciar um novo projeto de Android Studio**.
1. No ecrã **Criar Projeto Android**, modifique os campos predefinidos, se necessário, e clique em **Seguinte**.
1. Na tela **dispositivos Android de destino** , use o seletor suspenso para escolher **API 22** ou posterior e clique em **Avançar**.
1. Selecione **Atividade Vazia** e clique em **Seguinte**.
1. Desmarque **Retrocompatibilidade** e clique em **Concluir**.

## <a name="add-the-initial-code"></a>Adicionar o código inicial

### <a name="create-the-ui"></a>Criar a interface do usuário

Abra *activity_main. xml*. No editor de layout, selecione a guia **texto** e, em seguida, substitua o conteúdo pelo código a seguir.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Criar a classe principal

Abra *MainActivity. java* e substitua as instruções de `import` existentes pelo código a seguir.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Em seguida, substitua o conteúdo da classe **MainActivity** pelo código a seguir. Isso cria um manipulador de eventos no **botão** que inicia uma nova atividade para permitir que o usuário selecione uma imagem. Ele exibe a imagem no **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Experimentar a aplicação

Comente a chamada para **detectAndFrame** no método **onActivityResult** . Em seguida, pressione **executar** no menu para testar seu aplicativo. Quando o aplicativo for aberto, seja em um emulador ou em um dispositivo conectado, clique em **procurar** na parte inferior. A caixa de diálogo seleção de arquivo do dispositivo deve ser exibida. Escolha uma imagem e verifique se ela é exibida na janela. Em seguida, feche o aplicativo e avance para a próxima etapa.

![Captura de ecrã do Android de uma fotografia com rostos](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Adicionar o SDK facial

### <a name="add-the-gradle-dependency"></a>Adicionar a dependência gradle

No painel **Projeto**, utilize o seletor de lista pendente para selecionar **Android**. Expanda **Scripts de Gradle** e abra *build.gradle (Module: app)* . Adicione uma dependência para a biblioteca de cliente do Face `com.microsoft.projectoxford:face:1.4.3`, conforme mostrado na captura de ecrã abaixo, e clique em **Sincronizar Agora**.

![Captura de ecrã do Android Studio do ficheiro build.gradle da Aplicação](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Adicionar o código do projeto relacionado à face

Volte para **MainActivity. java** e adicione as seguintes instruções de `import`:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Em seguida, insira o seguinte código na classe **MainActivity** , acima do método **OnCreate** :

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

No painel **Projeto**, expanda **aplicação**, em seguida, **manifestos**, e abra *AndroidManifest.xml*. Insira o seguinte elemento como um subordinado direto do elemento `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detectar rostos

Seu aplicativo irá detectar faces chamando o método **faceClient. face. DetectWithStreamAsync** , que ENCAPSULA a API REST de [detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e retorna uma lista de instâncias de **face** .

Cada **face** retornada inclui um retângulo para indicar seu local, combinado com uma série de atributos de face opcionais. Neste exemplo, somente os retângulos de face são solicitados.

Insira os dois métodos a seguir na classe **MainActivity** . Quando a detecção facial for concluída, o aplicativo chamará o método **drawFaceRectanglesOnBitmap** para modificar o **ImageView**. Esse método será definido em seguida.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais nos pontos

Insira o seguinte método auxiliar na classe **MainActivity** . Esse método desenha um retângulo em volta de cada face detectada, usando as coordenadas de retângulo de cada instância de **face** .

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Por fim, remova a marca de comentário da chamada para o método **detectAndFrame** em **onActivityResult**.

## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem com um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Você deve ver um retângulo vermelho em cada uma das faces na imagem.

![Captura de tela do Android de rostos com retângulos vermelhos desenhados em torno deles](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu o processo básico para usar o SDK do Java API de Detecção Facial e criou um aplicativo para detectar e estruturar rostos em uma imagem. Em seguida, saiba mais sobre os detalhes da detecção facial.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
