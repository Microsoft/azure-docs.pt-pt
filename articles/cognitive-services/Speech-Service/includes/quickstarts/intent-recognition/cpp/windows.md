---
title: 'Início rápido: reconhecer fala, intenções e entidades, C++ -serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d421de69f856790b89c866a6c8b7221e9214aef4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772889"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se este for seu primeiro C++ projeto, use este guia para <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">criar um projeto de exemplo vazio</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Instale o SDK de fala para seu ambiente de desenvolvimento</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar um aplicativo LUIS para reconhecimento de intenção

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

Em seguida, abra o projeto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto. Lembre-se de que você criou um método assíncrono chamado `recognizeIntent()`.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `IntentRecognizer`, você precisa criar uma configuração que usa a chave e o local para o recurso de previsão LUIS. 

> [!IMPORTANT]
> A chave inicial e as chaves de criação não funcionarão. Você deve usar sua chave de previsão e o local que você criou anteriormente. Para obter mais informações, consulte [criar um aplicativo Luis para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition). 

Insira esse código no método `recognizeIntent()`. Certifique-se de atualizar esses valores:

* Substitua `"YourLanguageUnderstandingSubscriptionKey"` pela sua chave de previsão LUIS. 
* Substitua `"YourLanguageUnderstandingServiceRegion"` pelo local do LUIS. 

>[!TIP]
> Se você precisar de ajuda para encontrar esses valores, consulte [criar um aplicativo Luis para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Este exemplo usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Insira esse código no método `recognizeIntent()`, logo abaixo da sua configuração de fala.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicionar uma LanguageUnderstandingModel e tentativas

Você precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as tentativas que você deseja que sejam reconhecidos. Vamos usar as intenções do domínio predefinido para a automação doméstica. 

Insira este código abaixo do `IntentRecognizer`. Certifique-se de substituir `"YourLanguageUnderstandingAppId"` pela ID do aplicativo LUIS. 

>[!TIP]
> Se precisar de ajuda para encontrar esse valor, consulte [criar um aplicativo Luis para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

A partir do objeto `IntentRecognizer`, você chamará o método `RecognizeOnceAsync()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala. Para simplificar, aguardaremos que o futuro voltasse a ser concluído.

Insira este código abaixo do seu modelo:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no console.

Insira este código abaixo `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, seu código deve ter a seguinte aparência:  

> [!NOTE]
> Adicionamos alguns comentários a esta versão.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

1. **Compilar o código** -na barra de menus do Visual Studio, escolha **Compilar** > **Compilar solução**.
2. **Inicie seu aplicativo** -na barra de menus, escolha **depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar reconhecimento** -ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao serviço de fala, transcrita como texto e renderizada no console do.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]