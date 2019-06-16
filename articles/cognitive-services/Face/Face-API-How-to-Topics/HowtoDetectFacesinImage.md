---
title: Detetar rostos numa imagem - Face API
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar os dados de várias retornados pela funcionalidade de deteção de rostos.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124556"
---
# <a name="get-face-detection-data"></a>Obter dados de deteção de rostos

Este guia demonstra como utilizar a deteção de rostos para extrair atributos como género, idade ou a posição de uma determinada imagem. Os fragmentos de código neste guia são escritos em C# com a biblioteca de cliente de API de Face de serviços cognitivos do Azure. A mesma funcionalidade está disponível através da [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostra-lhe como para:

- Obter as localizações e as dimensões de faces numa imagem.
- Obter as localizações de vários marcos de rostos, como pupils, mergulho e boca, numa imagem.
- Acho que o sexo, idade, emoção e outros atributos de um rosto detetado.

## <a name="setup"></a>Configurar

Este guia assume que já criada uma [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objeto, chamado `faceClient`, com um URL de chave e o ponto final da subscrição de rostos. A partir daqui, pode utilizar a funcionalidade de deteção de face ao chamar qualquer um [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), que é utilizada neste guia, ou [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Para obter instruções sobre como configurar esta funcionalidade, consulte a [detetar rostos início rápido para o C# ](../quickstarts/csharp-detect-sdk.md).

Este guia enfoca as especificidades da chamada de deteção, como argumentos de que pode passar e o que pode fazer com os dados retornados. Recomendamos que consultar apenas os recursos que necessários. Cada operação demora mais tempo a concluir.

## <a name="get-basic-face-data"></a>Obter dados do mostrador básico

Para encontrar rostos e obter as respetivas localizações numa imagem, chame o método com o _returnFaceId_ parâmetro definido como **verdadeiro**. Esta é a predefinição.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Pode consultar retornado [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objetos para seus exclusivo, IDs e um retângulo que fornece as coordenadas de pixel do mostrador da.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Para obter informações sobre como analisar a localização e as dimensões do mostrador da, consulte [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Geralmente, esse retângulo contém os olhos, sobrancelhas, mergulho e boca. Parte superior do head e chin ouvidos não são necessariamente incluídas. Para utilizar o retângulo da face ao recortar um cabeçalho de completado ou obter uma captura médio vertical, talvez para uma imagem do tipo de ID de fotografia, pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência do rosto

[Pontos de referência de rostos](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos de fácil de encontrar um rosto, como os pupils ou ponta o nariz. Para obter dados de ponto de referência do rosto, defina o _returnFaceLandmarks_ parâmetro para **verdadeiro**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

O código a seguir demonstra como pode recuperar as localizações dos mergulho e pupils:

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

Também pode utilizar dados de pontos de referência do rosto para calcular com precisão a direção do mostrador da. Por exemplo, pode definir a rotação do mostrador da como um vetor do centro da boca para o centro do olhar. O código a seguir calcula esse vetor:

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

Quando sabe a direção do mostrador da, pode girar o quadro de face retangular alinhá-lo mais corretamente. Para cortar rostos numa imagem, por meio de programação pode girar a imagem para que os rostos aparecem sempre vertical.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além de retângulos e pontos de referência, a API de deteção de rostos pode analisar vários atributos conceituais de um rosto. Para obter uma lista completa, consulte a [atributos faciais](../concepts/face-detection.md#attributes) secção conceitual.

Para analisar os atributos faciais, defina o _returnFaceAttributes_ parâmetro para uma lista de [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) valores.

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

Em seguida, obter referências para os dados retornados e fazer outras operações, de acordo com suas necessidades.

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

Para saber mais sobre cada um dos atributos, consulte a [atributos e deteção de rostos](../concepts/face-detection.md) guia conceitual.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a usar as diversas funcionalidades de deteção de rostos. Em seguida, integre esses recursos na sua aplicação, siga um tutorial aprofundado.

- [Tutorial: Criar um aplicativo do WPF para exibir dados de rostos numa imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial: Criar uma aplicação Android para detetar e rostos numa imagem de fotograma](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)