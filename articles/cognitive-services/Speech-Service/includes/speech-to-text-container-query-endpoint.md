---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422117"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Discurso-a-texto ou Discurso Personalizado-a-texto

O recipiente fornece APIs finais de consulta baseados na websocket, que são acedidos através do [SDK](../index.yml)do discurso . Por padrão, o SDK do Discurso utiliza serviços de fala online. Para utilizar o recipiente, é necessário alterar o método de inicialização.

> [!TIP]
> Ao utilizar o SDK do Discurso com recipientes, não precisa de fornecer a chave de subscrição de recursos do Discurso Azure ou um símbolo do portador da [autenticação](../rest-speech-to-text.md#authentication).

Veja os exemplos abaixo.

# <a name="c"></a>[C #](#tab/csharp)

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
