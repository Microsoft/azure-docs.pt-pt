---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d819eaa5c90e304a642efd3a6f2458cab4eefe7f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422152"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Instale o SDK de Discurso para o<span class="docon docon-navigate-external x-hidden-focus"></span>seu ambiente de desenvolvimento e crie um projeto de amostra vazia.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Abra o seu projeto

1. Abra o seu IDE preferido.
2. Crie um novo projeto `quickstart.py`e crie ficheiros chamados, e depois abra-o.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder `IntentRecognizer` inicializar um objeto, precisa de criar uma configuração que utilize a chave e localização para o seu recurso de previsão LUIS.

Insira `quickstart.py`este código em . Certifique-se de atualizar estes valores:

* Substitua-a `"YourLanguageUnderstandingSubscriptionKey"` pela sua chave de previsão LUIS.
* Substitua-a `"YourLanguageUnderstandingServiceRegion"` pela sua localização LUIS. Identificador da **região** de utilização da [região](https://aka.ms/speech/sdkregion)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Esta amostra constrói `SpeechConfig` o objeto utilizando a chave LUIS e a região. Para obter uma lista completa dos métodos disponíveis, consulte a [Aula de SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntençãoReconhecedor

Agora, vamos criar `IntentRecognizer`um. Insira este código logo abaixo da configuração do Discurso.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um Modelo e Intenções LanguageUnderstanding

Precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenções e adicionar as intenções que quer que seja reconhecida. Vamos usar as intenções do domínio pré-construído para a domótica.

Insira este `IntentRecognizer`código abaixo do seu . Certifique-se de `"YourLanguageUnderstandingAppId"` que substitui o seu ID da aplicação LUIS. 

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [Criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vais chamar o `recognize_once()` método. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Insira este código abaixo do seu modelo.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Abaixo da `recognize_once()`sua chamada para, adicione este código.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Verifique o seu código

Neste momento, o seu código deve ser assim.

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Executar a amostra a partir da consola ou no seu IDE:

```
python quickstart.py
```

Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
