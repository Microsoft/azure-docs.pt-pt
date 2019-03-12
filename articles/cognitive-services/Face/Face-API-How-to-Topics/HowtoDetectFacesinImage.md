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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588776"
---
# <a name="get-face-detection-data"></a>Obter dados de deteção de rostos

Este guia irá demonstrar como utilizar a deteção de rostos para extrair atributos como género, idade ou a posição de uma determinada imagem. Os fragmentos de código neste guia são escritos em C# utilizando a biblioteca de cliente de Face API, mas a mesma funcionalidade está disponível através do [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia irá mostrar como para:

- Obter as localizações e as dimensões de faces numa imagem.
- Obter as localizações de várias face pontos de referência (pupils, mergulho, boca e assim por diante) numa imagem.
- Acho que o sexo, idade e emoções e outros atributos de um rosto detetado.

## <a name="setup"></a>Configurar

Este guia assume que já Criei uma **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objeto, chamado `faceClient`, com um URL de chave e o ponto final da subscrição de rostos. A partir daqui, pode utilizar a funcionalidade de deteção de face ao chamar qualquer um **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (utilizada neste guia) ou **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Consulte a [detetar enfrenta o guia de introdução para C# ](../quickstarts/csharp-detect-sdk.md) para obter instruções sobre como configurar estas definições.

Este guia irá focar-se com as especificidades da chamada detetar&mdash;que pode passar de argumentos e o que pode fazer com os dados retornados. Recomendamos que apenas consultar os recursos que necessários, como cada operação demora mais tempo a concluir.

## <a name="get-basic-face-data"></a>Obter dados do mostrador básico

Para encontrar rostos e obter as respetivas localizações numa imagem, chame o método com o _returnFaceId_ parâmetro definido como **verdadeiro** (predefinição).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Retornado **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objetos podem ser consultados para seus IDs exclusivos e um retângulo que fornece as coordenadas de pixel do mostrador da.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Ver **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** para obter informações sobre como analisar a localização e as dimensões do mostrador da. Geralmente, esse retângulo contém os olhos, sobrancelhas, mergulho e boca; parte superior do head e chin ouvidos não estão necessariamente incluídas. Se pretende usar o retângulo da face para recortar um principal de completo ou de captura médio vertical (uma imagem de tipo fotografia do ID), poderá expandir o retângulo por uma margem certo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência do rosto

Pontos de referência do rosto são um conjunto de pontos de fácil de encontrar um rosto, como os pupils ou ponta no nariz. Pode obter dados de ponto de referência do rosto, definindo a _returnFaceLandmarks_ parâmetro para **verdadeiro**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Por predefinição, existem 27 pontos de referência predefinidos. A figura a seguir mostra todos os pontos de 27:

![Um diagrama de rostos com todos os 27 referências assinaladas como](../Images/landmarks.1.jpg)

Os pontos retornados são em unidades de pixels, tal como o quadro do retângulo de rostos. O código a seguir demonstra como pode recuperar as localizações dos mergulho e pupils:

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

Dados de pontos de referência do rosto também podem ser utilizados para calcular com precisão a direção do mostrador da. Por exemplo, podemos definir a rotação do mostrador da como um vetor do centro da boca para o centro do olhar. O código a seguir calcula esse vetor:

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

Saber a direção do mostrador da, pode, em seguida, girar o quadro de face retangular alinhá-lo mais corretamente. Se quiser recortar rostos numa imagem, pode girar por meio de programação na imagem para que os rostos aparecem sempre vertical.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além de retângulos e pontos de referência, a API de deteção de rostos pode analisar vários atributos conceituais de um rosto. Estas incluem:

- Idade
- Género
- Intensidade do sorriso
- Pelo facial
- Óculos
- Posição de principal 3D
- Emoções

> [!IMPORTANT]
> Esses atributos são previstos através da utilização de algoritmos de estatísticos e poderão não ser sempre precisos. Tenha cuidado quando tomar decisões com base nos dados de atributo.
>

Para analisar os atributos faciais, defina o _returnFaceAttributes_ parâmetro para uma lista de **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** valores.

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

Em seguida, obter referências para os dados retornados e fazer ainda mais operações de acordo com suas necessidades.

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

Para saber mais sobre cada um dos atributos, consulte a [glossário](../Glossary.md).

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu como utilizar as diversas funcionalidades de deteção de rostos. Em seguida, veja a [glossário](../Glossary.md) para uma visão mais detalhada de recuperar os dados de rostos.

## <a name="related-topics"></a>Tópicos Relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
