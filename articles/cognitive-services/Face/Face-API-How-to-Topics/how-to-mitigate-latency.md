---
title: Como mitigar a latência ao utilizar o serviço Face
titleSuffix: Azure Cognitive Services
description: Aprenda a atenuar a latência ao utilizar o serviço Face.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706814"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Como: mitigar a latência ao utilizar o serviço Face

Pode encontrar latência quando utilizar o serviço Face. Latência refere-se a qualquer tipo de atraso que ocorra quando se comunica através de uma rede. Em geral, as possíveis causas de latência incluem:
- A distância física que cada pacote deve percorrer de origem para destino.
- Problemas com o meio de transmissão.
- Erros nos routers ou comutadores ao longo da via de transmissão.
- O tempo exigido por aplicações antivírus, firewalls e outros mecanismos de segurança para inspecionar os pacotes.
- Avarias nas aplicações de clientes ou servidores.

Este tópico fala sobre possíveis causas de latência específicas para o uso dos Serviços Cognitivos Azure, e como você pode mitigar estas causas.

> [!NOTE]
> A Azure Cognitive Services não fornece qualquer Acordo de Nível de Serviço (SLA) relativo à latência.

## <a name="possible-causes-of-latency"></a>Possíveis causas de latência

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Ligação lenta entre o Serviço Cognitivo e um URL remoto

Alguns Serviços Cognitivos Azure fornecem métodos que obtêm dados a partir de um URL remoto que fornece. Por exemplo, quando ligar para o [método DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço Face, pode especificar o URL de uma imagem na qual o serviço tenta detetar rostos.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

O serviço Face deve então descarregar a imagem a partir do servidor remoto. Se a ligação do serviço Face ao servidor remoto for lenta, isso afetará o tempo de resposta do método Detect.

Para atenuar isto, considere [guardar a imagem no Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Por exemplo:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Tamanho de upload grande

Alguns Serviços Cognitivos Azure fornecem métodos que obtêm dados de um ficheiro que você carrega. Por exemplo, quando ligar para o [método DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço Face, pode carregar uma imagem na qual o serviço tenta detetar rostos.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Se o ficheiro para carregar for grande, isso afetará o tempo de resposta do `DetectWithStreamAsync` método, pelas seguintes razões:
- Demora mais tempo a carregar o ficheiro.
- O serviço demora mais tempo a processar o ficheiro, proporcionalmente ao tamanho do ficheiro.

Mitigações:
- Considere [guardar a imagem no Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Por exemplo:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Considere carregar um ficheiro menor.
    - Consulte as diretrizes relativas aos dados de [entrada para deteção facial](../concepts/face-detection.md#input-data) e [dados de entrada para reconhecimento facial](../concepts/face-recognition.md#input-data).
    - Para a deteção facial, ao utilizar o modelo de `DetectionModel.Detection01` deteção, a redução do tamanho do ficheiro de imagem aumentará a velocidade de processamento. Ao utilizar o modelo de deteção, a redução do tamanho do ficheiro de `DetectionModel.Detection02` imagem só aumentará a velocidade de processamento se o ficheiro de imagem for inferior a 1920x1080.
    - Para reconhecimento facial, a redução do tamanho do rosto para 200x200 pixels não afeta a precisão do modelo de reconhecimento.
    - O desempenho do `DetectWithUrlAsync` e `DetectWithStreamAsync` métodos também depende de quantos rostos estão numa imagem. O serviço Face pode devolver até 100 rostos para uma imagem. Os rostos são classificados pelo tamanho do retângulo facial de grande a pequeno.
    - Se precisar de ligar para vários métodos de serviço, considere chamá-los em paralelo se o design da sua aplicação o permitir. Por exemplo, se precisar de detetar rostos em duas imagens para realizar uma comparação facial:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Ligação lenta entre o seu recurso computacional e o serviço Face

Se o seu computador tiver uma ligação lenta ao serviço Face, isso afetará o tempo de resposta dos métodos de serviço.

Mitigações:
- Quando criar a subscrição Face, certifique-se de escolher a região mais próxima do local onde a sua aplicação está hospedada.
- Se precisar de ligar para vários métodos de serviço, considere chamá-los em paralelo se o design da sua aplicação o permitir. Consulte a secção anterior para obter um exemplo.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a atenuar a latência ao utilizar o serviço Face. Em seguida, aprenda a escalar desde os objetos persongroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList, respectivamente.

> [!div class="nextstepaction"]
> [Exemplo: Utilize a característica em larga escala](how-to-use-large-scale.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)