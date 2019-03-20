---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57891367"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Os serviços cognitivos [SDK de voz](~/articles/cognitive-services/speech-service/speech-sdk.md) fornece a forma mais simples de usar **voz em texto** em seu aplicativo com funcionalidade completa.

1. Criar uma configuração de voz e fornecer uma chave de subscrição do serviço de voz (ou um token de autorização) e um [região](~/articles/cognitive-services/speech-service/regions.md) como parâmetros. Altere a configuração, conforme necessário. Por exemplo, fornecer um ponto final personalizado para especificar um ponto de extremidade de serviço não padrão, ou selecione o idioma falado de entrada ou formato de saída.

1. Crie um reconhecedor de voz da configuração de voz. Forneça uma configuração de áudio se pretender que a reconhecer a partir de uma origem diferente de seu microfone padrão (por exemplo, o fluxo de áudio ou o arquivo de áudio).

1. Obstruir os eventos para a operação assíncrona, se assim o desejar. O reconhecedor, em seguida, chama seus manipuladores de eventos quando tem resultados intermediárias e finais. Caso contrário, seu aplicativo recebe apenas um resultado final transcrição.

1. Inicie o reconhecimento. Para o reconhecimento de única, como o reconhecimento de comando ou uma consulta, utilize o `RecognizeOnceAsync()` (ou equivalente de idioma) método. Este método devolve a primeira expressão reconhecido. Para o reconhecimento de longa execução, como de transcrição, utilize o `StartContinuousRecognitionAsync()` (ou equivalente de idioma) método. Obstruir os eventos para resultados de reconhecimento assíncrono.

Consulte os seguintes fragmentos de código para cenários de reconhecimento de fala que utilizam o SDK de voz.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
