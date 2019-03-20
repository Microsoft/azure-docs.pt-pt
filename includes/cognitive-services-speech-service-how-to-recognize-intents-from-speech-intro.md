---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57902163"
---
<!-- N.B. no header, language-agnostic -->

Os serviços cognitivos da Microsoft [SDK de voz](~/articles/cognitive-services/speech-service/speech-sdk.md) fornece uma forma de reconhecer **objetivos do discurso** e é suportada pelos serviços cognitivos [o serviço de compreensão de idiomas (LUIS)](https://www.luis.ai/home).

1. Criar uma configuração de voz com uma chave de subscrição de LUIS e [região](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) como parâmetros. Denomina-se a chave de subscrição de LUIS **chave de ponto final** na documentação do serviço. Não é possível utilizar o LUIS chave de criação. (Consulte a observação posteriormente nesta secção).

1. Crie um reconhecedor intenção da configuração de voz. Forneça uma configuração de áudio se pretender que a reconhecer a partir de uma origem diferente de seu microfone padrão (por exemplo, o fluxo de áudio ou o arquivo de áudio).

1. Obter o modelo de compreensão de idioma com base no seu **AppId**. Adicione intenções que necessitar.

1. Obstruir os eventos para a operação assíncrona, se assim o desejar. O reconhecedor, em seguida, chama seus manipuladores de eventos quando tem resultados intermediárias e finais (inclui intenções). Se não resolvem os eventos, o seu aplicativo recebe apenas um resultado final transcrição.

1. Inicie o reconhecimento da intenção. Para o reconhecimento de única, como o reconhecimento de comando ou uma consulta, utilize o `RecognizeOnceAsync()` método. Este método devolve a primeira expressão reconhecido. Para o reconhecimento de longa execução, utilize o `StartContinuousRecognitionAsync()` método. Obstruir os eventos para resultados de reconhecimento assíncrono.

Consulte os seguintes fragmentos de código para cenários de reconhecimento da intenção que utilizam o SDK de voz. Substitua os valores no exemplo a sua própria chave de subscrição de LUIS (chave de ponto final), o [região da sua subscrição](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)e o **AppId** do seu modelo de intenção.

> [!NOTE]
> Ao contrário de outros serviços suportados pelo SDK de voz, reconhecimento da intenção requer uma chave de subscrição específica (chave de ponto final de LUIS). Para obter informações sobre a tecnologia de reconhecimento da intenção, consulte a [Web site do LUIS](https://www.luis.ai). Para obter informações sobre como adquirir o **chave de ponto final**, consulte [crie uma chave de ponto final do LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
