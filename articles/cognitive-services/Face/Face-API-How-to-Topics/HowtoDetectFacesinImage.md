---
title: Detectar faces em um rosto de imagem
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como usar a detecção facial para extrair atributos como gênero, idade ou pose de uma determinada imagem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169881"
---
# <a name="get-face-detection-data"></a>Obter dados de detecção facial

Este guia demonstra como usar a detecção facial para extrair atributos como gênero, idade ou pose de uma determinada imagem. Os trechos de código neste guia são escritos no C# usando a biblioteca de cliente facial dos serviços cognitivas do Azure. A mesma funcionalidade está disponível por meio da [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostra como:

- Obter os locais e as dimensões de faces em uma imagem.
- Obtenha os locais de vários pontos de referência de face, como Pupils, nariz e boca, em uma imagem.
- Adivinhe o gênero, a idade, a emoção e outros atributos de uma face detectada.

## <a name="setup"></a>Configuração

Este guia pressupõe que você já construiu um objeto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) , chamado `faceClient`, com uma chave de assinatura facial e uma URL de ponto de extremidade. A partir daqui, você pode usar o recurso de detecção facial chamando o [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), que é usado neste guia ou [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Para obter instruções sobre como configurar esse recurso, siga um dos guias de início rápido.

Este guia concentra-se nas especificidades da chamada de detecção, como os argumentos que você pode passar e o que pode fazer com os dados retornados. Recomendamos que você consulte apenas os recursos necessários. Cada operação leva mais tempo para ser concluída.

## <a name="get-basic-face-data"></a>Obter dados básicos de face

Para localizar faces e obter seus locais em uma imagem, chame o método com o parâmetro _returnFaceId_ definido como **true**. Esta é a predefinição.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Você pode consultar os objetos [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) retornados para suas IDs exclusivas e um retângulo que fornece as coordenadas de pixel da face.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Para obter informações sobre como analisar o local e as dimensões da face, consulte [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Normalmente, esse retângulo contém os olhos, os eyebrow, o nariz e a boca. A parte superior de Head, ouvidos e Chin não estão necessariamente incluídas. Para usar o retângulo de face para cortar uma cabeça completa ou obter um retrato de média, talvez para uma imagem de tipo ID de foto, você pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência facial

As dicas de [referência](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos fáceis de encontrar em uma face, como o Pupils ou a gorjeta do nariz. Para obter dados de referência de face, defina o parâmetro _returnFaceLandmarks_ como **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

O código a seguir demonstra como você pode recuperar os locais do nariz e do Pupils:

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

Você também pode usar dados de pontos de referência de face para calcular com precisão a direção da face. Por exemplo, você pode definir a rotação da face como um vetor do centro da boca para o centro dos olhos. O código a seguir calcula esse vetor:

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

Quando você sabe a direção da face, pode girar o quadro de rosto Retangular para alinhá-lo mais adequadamente. Para cortar faces em uma imagem, você pode girar a imagem de forma programática para que as faces sempre apareçam na vertical.

## <a name="get-face-attributes"></a>Obter atributos de face

Além de retângulos de face e pontos de referência, a API de detecção facial pode analisar vários atributos conceituais de uma face. Para obter uma lista completa, consulte a seção conceitual de [atributos de rosto](../concepts/face-detection.md#attributes) .

Para analisar os atributos de face, defina o parâmetro _returnFaceAttributes_ como uma lista de valores de [Enumeração FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

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

Em seguida, obtenha referências aos dados retornados e realize mais operações de acordo com suas necessidades.

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

Para saber mais sobre cada um dos atributos, consulte o guia conceitual de [detecção e atributos de rosto](../concepts/face-detection.md) .

## <a name="next-steps"></a>Passos seguintes

Neste guia, você aprendeu a usar as várias funcionalidades de detecção facial. Em seguida, integre esses recursos ao seu aplicativo seguindo um tutorial detalhado.

- [Tutorial: criar um aplicativo WPF para exibir dados de face em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial: criar um aplicativo Android para detectar e estruturar rostos em uma imagem](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)