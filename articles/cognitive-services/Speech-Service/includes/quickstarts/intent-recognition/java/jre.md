---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 6bd276f07f75ecad21ffb0bf19150f6158eb7b7c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156542"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se este é o seu primeiro projeto Java (JRE), use este guia para <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=jre" target="_blank">criar um projeto <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>de amostra vazia.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Instale o SDK de <span class="docon docon-navigate-external x-hidden-focus"> </span>Discurso para o seu ambiente </a>de desenvolvimento.

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Abra o seu projeto

1. Abra o seu IDE preferido.
2. Carregue o seu projeto e abra `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder inicializar um `IntentRecognizer` objeto, precisa de criar uma configuração que utilize a chave e localização para o seu recurso de previsão LUIS.  

Insira este código no bloco de tentativa /captura em `main()`. Certifique-se de atualizar estes valores:

* Substitua `"YourLanguageUnderstandingSubscriptionKey"` com a sua chave de previsão LUIS.
* Substitua `"YourLanguageUnderstandingServiceRegion"` pela sua localização LUIS. Use "Speech SDK Parameter" da [região](https://aka.ms/speech/sdkregion)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Esta amostra utiliza o método `FromSubscription()` para construir a `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte a [Aula de SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntençãoReconhecedor

Agora, vamos criar uma `IntentRecognizer`. Insira este código logo abaixo da configuração do Discurso.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um Modelo e Intenções LanguageUnderstanding

Precisa associar um `LanguageUnderstandingModel` ao reconhecimento de intenções, e adicionar as intenções que quer que seja reconhecida. Vamos usar as intenções do domínio pré-construído para a domótica.

Insira este código abaixo do seu `IntentRecognizer`. Certifique-se de que substitui `"YourLanguageUnderstandingAppId"` com o seu ID de aplicação LUIS.

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vai chamar o método `recognizeOnceAsync()`. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Insira este código abaixo do seu modelo:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Insira este código abaixo da sua chamada para `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Recursos de Lançamento

É importante libertar os recursos da fala quando acabares de os usar. Insira este código no final do bloco de tentativa / captura:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Prima <kbd>F11</kbd>, ou selecione **Executar** > **Debug**.
Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
