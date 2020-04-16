---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 54b5e3f0ed2fd10fa04305fdefbec725143f62c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421779"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Instale o SDK de Discurso para o<span class="docon docon-navigate-external x-hidden-focus"></span>seu ambiente de desenvolvimento um projeto de amostra sinuoso e de criação.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

Em seguida, abra o seu projeto no Estúdio Visual.

1. Lançar O Estúdio Visual 2019.
2. Carregue o seu `Program.cs`projeto e abra.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto. Tome nota de que criou um `RecognizeIntentAsync()`método de asincronização chamado .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder `IntentRecognizer` inicializar um objeto, precisa de criar uma configuração que utilize a chave e localização para o seu recurso de previsão LUIS.

> [!IMPORTANT]
> A sua chave de arranque e as chaves de autor não funcionarão. Deve usar a sua chave de previsão e localização que criou anteriormente. Para mais informações, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

Insira este `RecognizeIntentAsync()` código no método. Certifique-se de atualizar estes valores:

* Substitua-a `"YourLanguageUnderstandingSubscriptionKey"` pela sua chave de previsão LUIS.
* Substitua-a `"YourLanguageUnderstandingServiceRegion"` pela sua localização LUIS. Identificador **região** de utilização da [região.](https://aka.ms/speech/sdkregion)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Esta amostra `FromSubscription()` utiliza o `SpeechConfig`método para construir o . Para obter uma lista completa dos métodos disponíveis, consulte a [Aula de SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntençãoReconhecedor

Agora, vamos criar `IntentRecognizer`um. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este `RecognizeIntentAsync()` código no método, logo abaixo da configuração do Discurso.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um LanguageUnderstandingModel e intenções

Precisa associar um `LanguageUnderstandingModel` ao reconhecimento de intenções e adicionar as intenções que quer que seja reconhecido. Vamos usar as intenções do domínio pré-construído para a domótica. Insira este código na declaração de utilização da secção anterior. Certifique-se de `"YourLanguageUnderstandingAppId"` que substitui o seu ID da aplicação LUIS.

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vais chamar o `RecognizeOnceAsync()` método. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Dentro da declaração de utilização, adicione este código abaixo do seu modelo.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Resultados de reconhecimento de visualização (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir os resultados para consolar.

Dentro da declaração `RecognizeOnceAsync()`de utilização, abaixo, adicione este código:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço de Discurso.

1. **Compile o código** - A partir da barra de menu do Estúdio Visual, escolha **Build** > **Build Solution**.
2. **Inicie a sua aplicação** - A partir da barra de menus, escolha **Debug** > **Start Debugging** ou prima <kbd>F5</kbd>.
3. **Comece a reconhecer** - Vai instá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço da Fala, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
