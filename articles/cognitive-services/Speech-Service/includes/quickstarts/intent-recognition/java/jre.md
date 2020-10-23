---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8bcc3e41f87570822f48a111fe98d1fdaf72679f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470866"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Instale o Speech SDK para o seu <span class="docon docon-navigate-external x-hidden-focus"></span> ambiente de desenvolvimento e crie um projeto de amostra vazia.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Abra o seu projeto

1. Abra o seu IDE preferido.
2. Carregue o seu projeto e `Main.java` abra.

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Antes de poder inicializar um `IntentRecognizer` objeto, precisa de criar uma configuração que utilize a chave e a localização para o seu recurso de previsão LUIS.  

Insira este código no bloco de tentativa/captura `main()` em . Certifique-se de atualizar estes valores:

* `"YourLanguageUnderstandingSubscriptionKey"`Substitua-a pela sua chave de previsão LUIS.
* `"YourLanguageUnderstandingServiceRegion"`Substitua-o pela sua localização LUIS. Utilização **do identificador** da [região](https://aka.ms/speech/sdkregion)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Esta amostra utiliza o `FromSubscription()` método para construir o `SpeechConfig` . Para obter uma lista completa dos métodos disponíveis, consulte [a Aula deConfig da Fala](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true).

O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar `IntentRecognizer` um. Insira este código logo abaixo da configuração do Discurso.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um Modelo e Intenções De LínguaUnderstanding

Precisa associar um `LanguageUnderstandingModel` ao reconhecimento de intenções, e adicionar as intenções que deseja ser reconhecido. Vamos usar as intenções do domínio pré-construído para a domótica.

Insira este código abaixo do seu `IntentRecognizer` . Certifique-se de que substitui `"YourLanguageUnderstandingAppId"` pelo seu ID de aplicação LUIS.

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

Este exemplo utiliza a `addIntent()` função para adicionar individualmente intenções. Se quiser adicionar todas as intenções de um modelo, use `addAllIntents(model)` e passe o modelo.

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vais chamar o `recognizeOnceAsync()` método. Este método permite ao serviço de Discurso saber que está a enviar uma única frase para reconhecimento, e que uma vez que a frase é identificada para parar de reconhecer a fala.

Insira este código abaixo do seu modelo:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Insira este código abaixo da sua chamada para `recognizeOnceAsync()` .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Libertar Recursos

É importante libertar os recursos do discurso quando acabarmos de usá-los. Insira este código no final do bloco de tentativa/captura:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Prima <kbd>F11,</kbd>ou selecione **Run**  >  **Debug**.
Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
