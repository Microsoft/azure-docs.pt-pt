---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321052"
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

a esta chamada utilizando o [hospedeiro](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)do recipiente:

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

---
