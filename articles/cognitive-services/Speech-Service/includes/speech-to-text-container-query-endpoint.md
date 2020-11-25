---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996453"
---
O recipiente fornece APIs de consulta baseada em websocket, que são acedidos através do [SDK](../index.yml)de fala . Por padrão, o Speech SDK utiliza serviços de fala online. Para utilizar o recipiente, é necessário alterar o método de inicialização.

> [!TIP]
> Ao utilizar o SDK de discurso com recipientes, não precisa de fornecer a chave de subscrição de recursos Azure Speech [ou um símbolo do portador da autenticação](../rest-speech-to-text.md#authentication).

Veja os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Alterar a utilização desta chamada de inicialização em nuvem Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Para utilizar esta chamada com o [anfitrião](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)do recipiente:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Alterar a utilização desta chamada de inicialização em nuvem Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Para utilizar esta chamada com o [ponto final](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)do recipiente:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---