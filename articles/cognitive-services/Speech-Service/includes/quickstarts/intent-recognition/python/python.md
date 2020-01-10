---
title: 'Início rápido: reconhecer fala, intenções e entidades, serviço de fala em Python'
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
ms.openlocfilehash: f2a46b8a0868aaa1097dd7f1ae738a2a3e06e3b8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772910"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se este for seu primeiro projeto Python, use este guia para <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md" target="_blank">criar um projeto de exemplo vazio</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">Instale o SDK de fala para seu ambiente de desenvolvimento</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar um aplicativo LUIS para reconhecimento de intenção

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Abra seu projeto

1. Abra o IDE preferencial.
2. Crie um novo projeto e crie um arquivo chamado `quickstart.py`e, em seguida, abra-o.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `IntentRecognizer`, você precisa criar uma configuração que usa a chave e o local para o recurso de previsão LUIS.

Insira este código em `quickstart.py`. Certifique-se de atualizar esses valores:

* Substitua `"YourLanguageUnderstandingSubscriptionKey"` pela sua chave de previsão LUIS. 
* Substitua `"YourLanguageUnderstandingServiceRegion"` pelo local do LUIS. 

>[!TIP]
> Se você precisar de ajuda para encontrar esses valores, consulte [criar um aplicativo Luis para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Este exemplo constrói o objeto `SpeechConfig` usando a chave e a região LUIS. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Insira este código logo abaixo da sua configuração de fala.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicionar uma LanguageUnderstandingModel e tentativas

Você precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as tentativas que você deseja que sejam reconhecidos. Vamos usar as intenções do domínio predefinido para a automação doméstica. 

Insira este código abaixo do `IntentRecognizer`. Certifique-se de substituir `"YourLanguageUnderstandingAppId"` pela ID do aplicativo LUIS. 

>[!TIP]
> Se precisar de ajuda para encontrar esse valor, consulte [criar um aplicativo Luis para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

A partir do objeto `IntentRecognizer`, você chamará o método `recognize_once()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala.

Insira este código abaixo do seu modelo:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no console.

Abaixo de sua chamada para `recognize_once()`, adicione este código: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, seu código deve ter a seguinte aparência:  

> [!NOTE]
> Adicionamos alguns comentários a esta versão.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Execute o exemplo do console do ou do no IDE:

```
python quickstart.py
```

Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
