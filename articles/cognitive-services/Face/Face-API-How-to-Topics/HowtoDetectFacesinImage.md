---
title: Detetar rostos numa imagem - Rosto
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como usar a deteção facial para extrair atributos como sexo, idade ou pose a partir de uma determinada imagem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 231f30f5532d0934ba41e591aa821d56b11d5856
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928008"
---
# <a name="get-face-detection-data"></a>Obtenha dados de deteção facial

Este guia demonstra como usar a deteção facial para extrair atributos como sexo, idade ou pose a partir de uma determinada imagem. Os fragmentos de código neste guia são escritos em C# utilizando a biblioteca de clientes Azure Cognitive Services Face. A mesma funcionalidade está disponível através da [API REST.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

Este guia mostra-lhe como:

- Obtenha as localizações e dimensões dos rostos numa imagem.
- Obtenha a localização de vários marcos faciais, como pupilas, nariz e boca, numa imagem.
- Adivinha o sexo, idade, emoção, e outros atributos de um rosto detetado.

## <a name="setup"></a>Configuração

Este guia pressupõe que já construiu um objeto [FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) `faceClient` nomeado, com uma chave de subscrição Face e URL de ponto final. A partir daqui, pode utilizar a função de deteção facial chamando [detectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), que é usado neste guia, ou [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Para obter instruções sobre como configurar esta função, siga um dos arranques rápidos.

Este guia centra-se nas especificidades da chamada Detect, como os argumentos que pode passar e o que pode fazer com os dados devolvidos. Recomendamos que se questione apenas para as funcionalidades de que necessita. Cada operação leva tempo adicional para ser concluída.

## <a name="get-basic-face-data"></a>Obtenha dados básicos do rosto

Para encontrar rostos e obter as suas localizações numa imagem, ligue para o método com o _parâmetroFaceId retornado_ definido para **verdadeiro**. Esta é a predefinição.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Pode consultar os objetos [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) devolvidos para os seus IDs únicos e um retângulo que dá as coordenadas de pixel do rosto.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Para obter informações sobre como analisar a localização e as dimensões do rosto, consulte [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Normalmente, este retângulo contém os olhos, sobrancelhas, nariz e boca. A parte superior da cabeça, orelhas e queixo não estão necessariamente incluídas. Para usar o retângulo facial para cortar uma cabeça completa ou obter um retrato a meio do tiro, talvez para uma imagem do tipo foto, você pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obtenha marcos de rosto

[Marcos faciais](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos fáceis de encontrar em um rosto, como as pupilas ou a ponta do nariz. Para obter dados de referência de rosto, desafie o parâmetro _de retornoFaceLandmarks_ para **verdadeiro**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

O seguinte código demonstra como pode recuperar a localização do nariz e das pupilas:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Também pode usar dados de marcos faciais para calcular com precisão a direção do rosto. Por exemplo, pode definir a rotação do rosto como um vetor do centro da boca para o centro dos olhos. O seguinte código calcula este vetor:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Quando souber a direção do rosto, pode rodar a face retangular para alinhá-la mais corretamente. Para cortar rostos numa imagem, pode rodar programáticamente a imagem para que os rostos apareçam sempre na vertical.

## <a name="get-face-attributes"></a>Obtenha atributos faciais

Além de retângulos e marcos faciais, a API de deteção facial pode analisar vários atributos conceptuais de um rosto. Para obter uma lista completa, consulte a secção conceptual [de atributos Face.](../concepts/face-detection.md#attributes)

Para analisar os atributos faciais, defina o parâmetro _DevoluçãoFaceAttributes_ para uma lista de valores [FaceAttributeType Enum.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Em seguida, obtenha referências aos dados devolvidos e faça mais operações de acordo com as suas necessidades.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Para saber mais sobre cada um dos atributos, consulte a [deteção](../concepts/face-detection.md) do Rosto e atribui o guia conceptual.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a utilizar as várias funcionalidades de deteção facial. Em seguida, integre estas funcionalidades na sua app seguindo um tutorial aprofundado.

- [Tutorial: Criar uma aplicação WPF para exibir dados faciais numa imagem](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)