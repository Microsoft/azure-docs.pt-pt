---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879033"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Discurso-a-texto ou Discurso Personalizado-a-texto

O recipiente fornece APIs finais de consulta baseados na websocket, que são acedidos através do [SDK](../index.yml)do discurso . Por padrão, o SDK do Discurso utiliza serviços de fala online. Para utilizar o recipiente, é necessário alterar o método de inicialização.

> [!TIP]
> Ao utilizar o SDK do Discurso com recipientes, não precisa de fornecer a chave de subscrição de recursos do Discurso Azure ou um símbolo do portador da [autenticação](../rest-speech-to-text.md#authentication).

Veja os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Mude de utilização desta chamada de inicialização azure-cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a esta chamada utilizando o [hospedeiro](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)do contentor:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Mude de utilização desta chamada de inicialização azure-cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a esta chamada utilizando o [hospedeiro](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)do contentor:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
