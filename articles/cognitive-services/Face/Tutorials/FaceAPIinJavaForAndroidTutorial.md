---
title: 'Tutorial: Detetar e enquadrar rostos numa imagem com o SDK Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma simples aplicação Android que utiliza o serviço Face para detetar e enquadrar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: dd986a7557f468b939aefe1da825c9834618047f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402923"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Tutorial: criar uma aplicação Android para detetar e enquadrar rostos numa imagem

Neste tutorial, você vai criar uma aplicação Android que usa o serviço Azure Face, através do Java SDK, para detetar rostos humanos numa imagem. A aplicação exibe uma imagem selecionada e desenha uma moldura em torno de cada rosto detetado.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação Android
> - Instale a biblioteca do cliente Face
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado

![Captura de ecrã do Android de uma fotografia com rostos enquadrados por um retângulo vermelho](../Images/android_getstarted2.1.PNG)

O código completo da amostra está disponível no repositório Face Android dos [Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-face-android-sample) no GitHub.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de subscrição Face. Você pode obter uma chave de subscrição de teste gratuito da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço Face e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT`chave e corda final de serviço, nomeada e, respectivamente.
- Qualquer edição do [Visual Studio 2015 ou 2017.](https://www.visualstudio.com/downloads/)
- [Android Studio](https://developer.android.com/studio/) com Nível API 22 ou mais tarde.

## <a name="create-the-android-studio-project"></a>Criar o projeto Android Studio

Siga estes passos para criar um novo projeto de aplicação Android.

1. No Android Studio, selecione **Iniciar um novo projeto Android Studio.**
1. No ecrã **Criar Projeto Android**, modifique os campos predefinidos, se necessário, e clique em **Seguinte**.
1. No ecrã **target Android Devices,** utilize o seletor de dropdown para escolher **API 22** ou mais tarde e, em seguida, clique em **Next**.
1. Selecione **Atividade Vazia** e clique em **Seguinte**.
1. Desmarque **Retrocompatibilidade** e clique em **Concluir**.

## <a name="add-the-initial-code"></a>Adicionar o código inicial

### <a name="create-the-ui"></a>Criar a UI

Abra *activity_main.xml.* No Editor de Layout, selecione o separador **Texto** e, em seguida, substitua o conteúdo pelo seguinte código.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Criar a classe principal

Abra *mainActivity.java* e substitua as declarações existentes `import` pelo seguinte código.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Em seguida, substitua o conteúdo da classe **MainActivity** pelo seguinte código. Isto cria um manipulador de eventos no **Botão** que inicia uma nova atividade para permitir ao utilizador selecionar uma imagem. Exibe a imagem no **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Experimente a aplicação

Comente a chamada para **detetar AndFrame** no método **onActivityResult.** Em seguida, pressione **Corra** no menu para testar a sua aplicação. Quando a aplicação abrir, seja num emulador ou num dispositivo conectado, clique na **Navegação** na parte inferior. O diálogo de seleção de ficheiros do dispositivo deve aparecer. Escolha uma imagem e verifique se exibe na janela. Em seguida, feche a app e avance para o próximo passo.

![Captura de ecrã do Android de uma fotografia com rostos](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Adicione o Face SDK

### <a name="add-the-gradle-dependency"></a>Adicione a dependência gradle

No painel **Projeto**, utilize o seletor de lista pendente para selecionar **Android**. Expanda **Scripts de Gradle** e abra *build.gradle (Module: app)*. Adicione uma dependência para a biblioteca de cliente do Face `com.microsoft.projectoxford:face:1.4.3`, conforme mostrado na captura de ecrã abaixo, e clique em **Sincronizar Agora**.

![Captura de ecrã do Android Studio do ficheiro build.gradle da Aplicação](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Adicione o código de projeto relacionado com o rosto

Volte para **MainActivity.java** e `import` adicione as seguintes declarações:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Em seguida, insira o seguinte código na classe **MainActivity,** acima do método **onCreate:**

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

No painel **Projeto**, expanda **aplicação**, em seguida, **manifestos**, e abra *AndroidManifest.xml*. Insira o seguinte elemento como um subordinado direto do elemento `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detetar rostos

A sua aplicação irá detetar rostos através do método **faceClient.Face.DetectWithStreamAsync,** que envolve a API [de Deteção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) DO REST e devolve uma lista de instâncias **Face.**

Cada **Face** devolvido inclui um retângulo para indicar a sua localização, combinado com uma série de atributos faciais opcionais. Neste exemplo, apenas são solicitados os retângulos faciais.

Insira os dois seguintes métodos na classe **MainActivity.** Quando a deteção facial completa, a aplicação chama o método **drawFaceRectanglesOnBitmap** para modificar o **ImageView**. Vai definir este método a seguir.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais

Insira o seguinte método de ajudante na classe **MainActivity.** Este método desenha um retângulo em torno de cada rosto detetado, utilizando as coordenadas de retângulo de cada instância **Face.**

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Por fim, não comente a chamada para o método **detetando AndFrame** no **onActivityResult**.

## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem com um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Devia ver um retângulo vermelho em cada um dos rostos na imagem.

![Imagem de rosto estoirado com retângulos vermelhos desenhados à sua volta](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o Face Java SDK para criar uma aplicação para detetar e enquadrar rostos numa imagem. Em seguida, saiba mais sobre os detalhes da deteção facial.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
