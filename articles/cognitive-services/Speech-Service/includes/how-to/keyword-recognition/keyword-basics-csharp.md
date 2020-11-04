---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305911"
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
> O exemplo aqui mostrado utiliza o reconhecimento de palavras-chave locais, uma vez que não requer um `SpeechConfig` objeto para o contexto de autenticação, e não contacta o back-end. No entanto, pode executar reconhecimento e verificação de [palavras-chave utilizando uma ligação contínua de back-end](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).