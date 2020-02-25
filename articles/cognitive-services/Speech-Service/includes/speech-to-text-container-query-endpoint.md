---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563282"
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
# <a name="python"></a>[python](#tab/python)

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
