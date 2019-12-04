---
title: 'Início rápido: reconhecer fala, intenções e entidades, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 0729bee2f01dbd230b6f4442fc8e429a4fef3e16
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795925"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
>
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Criar um aplicativo Reconhecimento vocal (LUIS) e obter uma chave de ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Abra seu projeto

Abra Quickstart.py em seu editor de Python.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `IntentRecognizer`, você precisa criar uma configuração que usa sua chave e região de ponto de extremidade LUIS. Insira este código em seguida.

Este exemplo constrói o objeto `SpeechConfig` usando a chave e a região LUIS. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).
O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

> [!NOTE]
> É importante usar a chave de ponto de extremidade LUIS e não as chaves de início ou de criação, já que apenas a chave do ponto de extremidade é válida para reconhecimento de fala e intenção. Consulte [criar um aplicativo Luis e obter uma chave de ponto de extremidade](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) para obter instruções sobre como obter a chave correta.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Insira este código logo abaixo da sua configuração de fala.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicionar uma LanguageUnderstandingModel e tentativas

Agora você precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as tentativas que você deseja que sejam reconhecidos.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

A partir do objeto `IntentRecognizer`, você chamará o método `recognize_once()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no console.

Dentro da instrução using, abaixo de sua chamada para `recognize_once()`, adicione este código: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, seu código deve ter a seguinte aparência:  
(Adicionamos alguns comentários a esta versão) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Execute o exemplo do console do ou do no IDE:

    ````
    python quickstart.py
    ````

Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
