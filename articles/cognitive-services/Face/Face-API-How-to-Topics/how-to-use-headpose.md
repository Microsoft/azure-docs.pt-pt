---
title: Utilizar o atributo HeadPose
titleSuffix: Azure Cognitive Services
description: Saiba como usar o atributo HeadPose para girar automaticamente o retângulo de face ou detectar gestos de cabeçalho em um feed de vídeo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169793"
---
# <a name="use-the-headpose-attribute"></a>Utilizar o atributo HeadPose

Neste guia, você verá como você pode usar o atributo HeadPose de uma face detectada para habilitar alguns cenários importantes.

## <a name="rotate-the-face-rectangle"></a>Girar o retângulo facial

O retângulo de face, retornado com cada face detectada, marca o local e o tamanho da face na imagem. Por padrão, o retângulo é sempre alinhado com a imagem (seus lados são vertical e horizontal); Isso pode ser ineficiente para rostos angulares de enquadramento. Em situações em que você deseja cortar programaticamente faces em uma imagem, é melhor poder girar o retângulo para cortar.

O aplicativo de exemplo [WPF de serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usa o atributo HeadPose para girar seus retângulos de face detectados.

### <a name="explore-the-sample-code"></a>Explore o código de exemplo

Você pode girar o retângulo de face programaticamente usando o atributo HeadPose. Se você especificar esse atributo ao detectar faces (consulte [como detectar rostos](HowtoDetectFacesinImage.md)), você poderá consultá-lo mais tarde. O método a seguir do aplicativo de [face de serviços cognitivas do WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usa uma lista de objetos **DetectedFace** e retorna uma lista de objetos **[face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** . **Face** aqui é uma classe personalizada que armazena dados de face, incluindo as coordenadas de retângulo atualizadas. Os novos valores são calculados para **superior**, **esquerdo**, **largura**e **altura**, e um novo campo **FaceAngle** especifica a rotação.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Exibir o retângulo atualizado

A partir daqui, você pode usar os objetos de **face** retornados em sua exibição. As linhas a seguir de [FaceDetectionPage. XAML](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) mostram como o novo retângulo é renderizado a partir desses dados:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detectar gestos de cabeçalho

Você pode detectar gestos de cabeça, como nodding e aperto de cabeça, acompanhando as alterações de HeadPose em tempo real. Você pode usar esse recurso como um detector de vida útil personalizado.

A detecção de tempo de vida é a tarefa de determinar que um assunto é uma pessoa real, e não uma representação de imagem ou de vídeo. Um detector de gestos de cabeçalho pode servir como uma maneira de ajudar a verificar a vida, especialmente em oposição a uma representação de imagem de uma pessoa.

> [!CAUTION]
> Para detectar gestos de cabeçalho em tempo real, você precisará chamar o API de Detecção Facial a uma taxa alta (mais de uma vez por segundo). Se você tiver uma assinatura de camada livre (F0), isso não será possível. Se você tiver uma assinatura de camada paga, certifique-se de ter calculado os custos de fazer chamadas de API rápidas para detecção de gestos de cabeçalho.

Consulte a [amostra de HeadPose facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) no GitHub para obter um exemplo de funcionamento da detecção de gestos de cabeçalho.

## <a name="next-steps"></a>Passos seguintes

Consulte o aplicativo do [WPF de serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) no GitHub para obter um exemplo de trabalho de retângulos de face girados. Ou então, consulte o aplicativo de [exemplo HeadPose facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) , que controla o atributo HeadPose em tempo real para detectar os movimentos de cabeça.