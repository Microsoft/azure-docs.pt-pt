---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 885751bf4f11312453245a02209f77b62b8344a4
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052835"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Instale o Speech SDK para o seu <span class="docon docon-navigate-external x-hidden-focus"></span> ambiente de desenvolvimento e crie um projeto de amostra vazia.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Visual Studio

Em seguida, abra o seu projeto no Visual Studio.

1. Lançamento Visual Studio 2019.
2. Carregue o seu projeto e `Program.cs` abra.

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto. Note que criou um método async chamado `RecognizeIntentAsync()` .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Antes de poder inicializar um `IntentRecognizer` objeto, precisa de criar uma configuração que utilize a chave e a localização para o seu recurso de previsão LUIS.

> [!IMPORTANT]
> A chave de arranque e as teclas de autoria não funcionarão. Deve usar a sua chave de previsão e localização que criou anteriormente. Para obter mais informações, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

Insira este código no `RecognizeIntentAsync()` método. Certifique-se de atualizar estes valores:

* `"YourLanguageUnderstandingSubscriptionKey"`Substitua-a pela sua chave de previsão LUIS.
* `"YourLanguageUnderstandingServiceRegion"`Substitua-o pela sua localização LUIS. Utilizar **o identificador** da região da [região.](../../../../regions.md)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Esta amostra utiliza o `FromSubscription()` método para construir o `SpeechConfig` . Para obter uma lista completa dos métodos disponíveis, consulte [a Aula deConfig da Fala](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar `IntentRecognizer` um. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no `RecognizeIntentAsync()` método, logo abaixo da configuração do Discurso.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um LanguageUnderstandingModel e intenções

Precisa associar um `LanguageUnderstandingModel` ao reconhecimento de intenções, e adicionar as intenções que quer que seja reconhecido. Vamos usar as intenções do domínio pré-construído para a domótica. Insira este código na declaração de utilização da secção anterior. Certifique-se de que substitui `"YourLanguageUnderstandingAppId"` pelo seu ID de aplicação LUIS.

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

Este exemplo utiliza a `AddIntent()` função para adicionar individualmente intenções. Se quiser adicionar todas as intenções de um modelo, use `AddAllIntents(model)` e passe o modelo. 

> [!NOTE]
> Pode criar um LanguageUnderstandingModel passando um URL de ponto final para o método FromEndpoint.
> A SDK da fala só suporta pontos finais LUIS v2.0, e os pontos finais LUIS v2.0 seguem sempre um destes dois padrões:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vais chamar o `RecognizeOnceAsync()` método. Este método permite ao serviço de Discurso saber que está a enviar uma única frase para reconhecimento, e que uma vez que a frase é identificada para parar de reconhecer a fala.

Dentro da declaração de utilização, adicione este código abaixo do seu modelo.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Resultados de reconhecimento de exibição (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir os resultados para consolar.

Dentro da declaração de utilização, `RecognizeOnceAsync()` abaixo, adicione este código:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço Speech.

1. **Compilar o código** - A partir da barra de menus do Estúdio Visual, escolha **Build**  >  **Build Solution**.
2. **Inicie a sua aplicação** - A partir da barra de menus, escolha **Debug**  >  **Start Debugging** ou prima <kbd>F5</kbd>.
3. **Comece a reconhecer** - Vai levá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço de discurso, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]