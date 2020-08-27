---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: debd1eda050e75ea2b9baed45e6e5af0f5a95c46
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944494"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Discurso-a-texto ou Discurso-a-Texto Personalizado

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
