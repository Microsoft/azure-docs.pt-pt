---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509457"
---
Em primeiro lugar, carregue o ficheiro do modelo de palavra-chave utilizando a `FromFile()` função estática, que devolve um `KeywordRecognitionModel` . Use o caminho para o `.table` ficheiro que descarregou do Speech Studio. Além disso, cria-se um `AudioConfig` microfone padrão e, em seguida, instantaneamente um novo `KeywordRecognizer` utilizando a configuração de áudio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Em seguida, executar o reconhecimento de palavras-chave é feito com uma chamada para `RecognizeOnceAsync()` passar o seu objeto modelo. Isto inicia uma sessão de reconhecimento de palavras-chave que dura até que a palavra-chave seja reconhecida. Assim, você geralmente usa este padrão de design em aplicações multi-roscadas, ou em casos em que você pode estar esperando por uma palavra de despertar indefinidamente.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> O exemplo aqui mostrado utiliza o reconhecimento de palavras-chave locais, uma vez que não requer um `SpeechConfig` objeto para o contexto de autenticação, e não contacta o back-end. No entanto, pode executar reconhecimento e verificação de [palavras-chave utilizando uma ligação contínua de back-end](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).