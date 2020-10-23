---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e920ea3cd91f6275c4cb52dc070b0df60312d525
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470946"
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

Para utilizar esta chamada com o [anfitrião](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet&preserve-view=true)do recipiente:

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

Para utilizar esta chamada com o [ponto final](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python&preserve-view=true)do recipiente:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
