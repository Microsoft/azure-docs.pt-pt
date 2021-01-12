---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 0cb27a8dc5685ce295c2ce30820734c4301e9dc6
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109523"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Instale o Speech SDK para o seu <span class="docon docon-navigate-external x-hidden-focus"></span> ambiente de desenvolvimento e crie um projeto de amostra vazia.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar uma app LUIS para reconhecimento de intenções

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Abra o seu projeto

1. Abra o seu IDE preferido.
2. Crie um novo projeto e crie um ficheiro `quickstart.py` chamado, em seguida, abra-o.

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Antes de poder inicializar um `IntentRecognizer` objeto, precisa de criar uma configuração que utilize a chave e a localização para o seu recurso de previsão LUIS.

Insira este código em `quickstart.py` . Certifique-se de atualizar estes valores:

* `"YourLanguageUnderstandingSubscriptionKey"`Substitua-a pela sua chave de previsão LUIS.
* `"YourLanguageUnderstandingServiceRegion"`Substitua-o pela sua localização LUIS. Utilização **do identificador** da [região](../../../../regions.md)

>[!TIP]
> Se precisar de ajuda para encontrar estes valores, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Esta amostra constrói o `SpeechConfig` objeto utilizando a chave e a região LUIS. Para obter uma lista completa dos métodos disponíveis, consulte [a Aula deConfig da Fala](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar `IntentRecognizer` um. Insira este código logo abaixo da configuração do Discurso.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um Modelo e Intenções De LínguaUnderstanding

Precisa associar um `LanguageUnderstandingModel` ao reconhecimento de intenções e adicionar as intenções que deseja reconhecidas. Vamos usar as intenções do domínio pré-construído para a domótica.

Insira este código abaixo do seu `IntentRecognizer` . Certifique-se de que substitui `"YourLanguageUnderstandingAppId"` pelo seu ID de aplicação LUIS. 

>[!TIP]
> Se precisar de ajuda para encontrar este valor, consulte [criar uma aplicação LUIS para reconhecimento de intenções.](#create-a-luis-app-for-intent-recognition)

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

Este exemplo utiliza a `add_intents()` função para adicionar uma lista de intenções explicitamente definidas. Se quiser adicionar todas as intenções de um modelo, use `add_all_intents(model)` e passe o modelo.

> [!NOTE]
> A SDK de discurso só suporta pontos finais LUIS v2.0.
> Tem de modificar manualmente o URL do ponto final v3.0 encontrado no campo de consulta de exemplo para utilizar um padrão URL v2.0.
> Os pontos finais LUIS v2.0 seguem sempre um destes dois padrões:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

Pelo `IntentRecognizer` objeto, vais chamar o `recognize_once()` método. Este método permite ao serviço de Discurso saber que está a enviar uma única frase para reconhecimento, e que uma vez que a frase é identificada para parar de reconhecer a fala.

Insira este código por baixo do seu modelo.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Abaixo a sua chamada para `recognize_once()` , adicione este código.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Verifique o seu código

Neste momento, o seu código deve ser assim.

> [!NOTE]
> Adicionámos alguns comentários a esta versão.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Execute a amostra da consola ou do seu IDE:

```
python quickstart.py
```

Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]