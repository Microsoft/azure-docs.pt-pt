---
title: 'Tutorial: Detetar e rostos numa imagem com o Android SDK de fotograma'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação Android simples que utiliza a API de rostos para detetar e quadro rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: a809a4de588a388c4be8ca6efa245450699eeb64
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441612"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Tutorial: Criar uma aplicação Android para detetar e rostos numa imagem de fotograma

Neste tutorial, irá criar uma aplicação Android simples que utiliza a API Face do Azure, através do SDK de Java, para detetar rostos humanos numa imagem. O aplicativo exibe uma imagem selecionada e desenha um quadro em torno de cada rosto detetado.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Criar uma aplicação Android
> - Instalar a biblioteca de cliente de API de rostos
> - Utilizar a biblioteca de cliente para detetar rostos numa imagem
> - Desenhar uma moldura em torno de cada rosto detetado

![Captura de ecrã do Android de uma fotografia com rostos enquadrados por um retângulo vermelho](../Images/android_getstarted2.1.PNG)

O código de exemplo completo está disponível na [Android de Face de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-face-android-sample) repositório no GitHub.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- [Android Studio](https://developer.android.com/studio/) com um nível de API 22 ou posterior (requerido pela biblioteca de cliente de rosto).

## <a name="create-the-android-studio-project"></a>Criar o projeto do Android Studio

Siga estes passos para criar um novo projeto de aplicação Android.

1. No Android Studio, selecione **inicie um novo projeto do Android Studio**.
1. No ecrã **Criar Projeto Android**, modifique os campos predefinidos, se necessário, e clique em **Seguinte**.
1. Sobre o **dispositivos Android de destino** ecrã, utilize o Seletor de lista pendente para escolher **22 de API** ou posterior, em seguida, clique em **seguinte**.
1. Selecione **Atividade Vazia** e clique em **Seguinte**.
1. Desmarque **Retrocompatibilidade** e clique em **Concluir**.

## <a name="add-the-initial-code"></a>Adicionar o código inicial

### <a name="create-the-ui"></a>Criar a interface do Usuário

Open *ctivity_main*. No Editor do Layout, selecione o **texto** separador, em seguida, substitua os conteúdos com o código a seguir.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>Criar a classe principal

Open *mainactivity. Java* e substitua o existente `import` instruções com o código a seguir.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

Em seguida, substitua o conteúdo do **MainActivity** classe com o código a seguir. Esta ação cria um manipulador de eventos sobre o **botão** que inicia uma nova atividade para permitir ao utilizador selecionar uma imagem. Ele exibe a imagem dos **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>Experimente a aplicação

Comente a chamada para **detectAndFrame** no **onActivityResult** método. Em seguida, prima **executar** no menu para testar a sua aplicação. Quando a aplicação abre-se, num emulador ou um dispositivo ligado, clique nas **procurar** na parte inferior. Deverá aparecer a caixa de diálogo de seleção de arquivo do dispositivo. Escolha uma imagem e certifique-se de que ele exibe na janela. Em seguida, feche a aplicação e avance para o passo seguinte.

![Captura de ecrã do Android de uma fotografia com rostos](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Adicionar o SDK de rostos

### <a name="add-the-gradle-dependency"></a>Adicione a dependência de Gradle

No painel **Projeto**, utilize o seletor de lista pendente para selecionar **Android**. Expanda **Scripts de Gradle** e abra *build.gradle (Module: app)* . Adicione uma dependência para a biblioteca de cliente do Face `com.microsoft.projectoxford:face:1.4.3`, conforme mostrado na captura de ecrã abaixo, e clique em **Sincronizar Agora**.

![Captura de ecrã do Android Studio do ficheiro build.gradle da Aplicação](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Adicione o código de projeto relacionados com a Face

Volte ao **mainactivity. Java** e adicione o seguinte `import` instruções:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

Em seguida, insira o seguinte código no **MainActivity** classe superior a **onCreate** método:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

Terá de substituir `<Subscription Key>` com a sua chave de subscrição. Além disso, substitua `<API endpoint>` ao ponto final da Face API, utilizando o identificador de região adequada para a sua chave (consulte o [documentos da API de rostos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todos os pontos finais de região). Chaves de subscrição de avaliação gratuita são geradas na **westus** região.

No painel **Projeto**, expanda **aplicação**, em seguida, **manifestos**, e abra *AndroidManifest.xml*. Insira o seguinte elemento como um subordinado direto do elemento `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>Carregar a imagem e detetar rostos

A aplicação irá detetar rostos ao chamar o **faceClient.Face.DetectWithStreamAsync** método, que encapsula o [detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API e devolve uma lista de **Face** instâncias.

Cada devolvido **Face** inclui um retângulo para indicar a localização, combinada com uma série de atributos faciais opcional. Neste exemplo, apenas os retângulos são solicitados.

Insira os seguintes dois métodos para o **MainActivity** classe. Tenha em atenção que, quando é concluída a deteção de rostos, a aplicação chama o **drawFaceRectanglesOnBitmap** método para modificar a **ImageView**. Em seguida, vai definir esse método.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais nos pontos

Insira o seguinte método de programa auxiliar para o **MainActivity** classe. Esse método desenha um retângulo em torno de cada rosto detetado com as coordenadas do retângulo de cada **Face** instância.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

Por fim, remova os comentários a chamada para o **detectAndFrame** método na **onActivityResult**.

## <a name="run-the-app"></a>Executar a aplicação

Execute a aplicação e procure uma imagem com um rosto. Aguarde alguns segundos para permitir que o serviço Face responda. Deverá ver um retângulo vermelho em cada um dos rostos na imagem.

![Captura de ecrã Android de faces com retângulos vermelhos desenhados em torno deles](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber o processo básico para utilizar o SDK de Java API Face e criou uma aplicação para detetar e quadro rostos numa imagem. Em seguida, saiba mais sobre os detalhes da deteção de rostos.

> [!div class="nextstepaction"]
> [Como Detetar Rostos numa Imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
