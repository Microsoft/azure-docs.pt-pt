---
title: Consultar ponto de extremidade do contêiner de conversão de fala em texto
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132617"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Conversão de fala em texto ou Fala Personalizada em texto

O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio do [SDK de fala](../index.md). Por padrão, o SDK de fala usa os serviços de fala online. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o SDK de fala com contêineres, você não precisa fornecer a chave de assinatura de recurso de fala do Azure [ou um token de portador de autenticação](../rest-speech-to-text.md#authentication).

Consulte os exemplos abaixo.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Alterar do uso desta chamada de inicialização do Azure-cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para essa chamada usando o [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)do contêiner:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Alterar do uso desta chamada de inicialização do Azure-cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

para essa chamada usando o [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)do contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
