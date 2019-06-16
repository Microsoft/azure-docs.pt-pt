---
title: Usar o atributo HeadPose
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o atributo HeadPose para girar automaticamente o retângulo da face ou detetar gestos principais numa transmissão de vídeo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058573"
---
# <a name="use-the-headpose-attribute"></a>Usar o atributo HeadPose

Neste guia, verá como pode utilizar o atributo HeadPose de um rosto detetado para permitir alguns cenários importantes.

## <a name="rotate-the-face-rectangle"></a>Rodar o retângulo de rostos

O retângulo de rostos, devolvido com todos os rostos detetados, marca a localização e tamanho de rosto na imagem. Por predefinição, o retângulo é sempre alinhado com a imagem (são de suas laterais vertical e horizontal); Isso pode ser ineficaz para inclinado rostos de delimitação de quadros. Em situações em que deseja programaticamente recortar rostos numa imagem, é melhor poder girar o retângulo para cortar.

O [WPF de Face de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplicação de exemplo utiliza o atributo HeadPose para girar seus retângulos detetado.

### <a name="explore-the-sample-code"></a>Explore o código de exemplo

Por meio de programação, pode girar o retângulo de rostos, utilizando o atributo HeadPose. Se especificar este atributo ao detetar rostos (consulte [como detetar rostos](HowtoDetectFacesinImage.md)), poderá consultá-los mais tarde. O método seguinte a partir da [WPF de Face de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplicação utiliza uma lista de **DetectedFace** objetos e devolve uma lista de **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objetos. **Face** aqui é uma classe personalizada que armazena enfrenta dados, incluindo as coordenadas do retângulo atualizado. Novos valores são calculados para **top**, **esquerdo**, **largura**, e **altura**e um novo campo **FaceAngle**Especifica a rotação.

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

### <a name="display-the-updated-rectangle"></a>Apresentar o retângulo atualizado

A partir daqui, pode utilizar o retornado **Face** objetos no seu ecrã. As seguintes linhas de [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) Mostrar como o novo rectangle é composto a partir destes dados:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detetar gestos principais

Pode detectar principais gestos, como nodding e head abanar através da monitorização de alterações de HeadPose em tempo real. Pode utilizar esta funcionalidade como um detetor de liveness personalizado.

Deteção de liveness é a tarefa de determinar que um assunto é uma pessoa real e não uma representação de imagem ou vídeo. Detetor de gesto principal pode servir como uma forma de ajudar a verificar liveness, especialmente em vez de uma representação de imagem de uma pessoa.

> [!CAUTION]
> Para detectar gestos principais em tempo real, terá de chamar a API Face a uma taxa elevada (mais do que uma vez por segundo). Se tiver uma subscrição gratuita-escalão (f0), isso não é possível. Se tiver uma subscrição de escalão pago, certificar-se de que já calculado os custos de API rápida de fazer chamadas para head gestos de deteção.

Consulte a [exemplo de HeadPose de API de rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) no GitHub para um exemplo de cabeça gestos de deteção.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [WPF de Face de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) aplicação no GitHub para um exemplo de retângulos girado. Em alternativa, consulte a [exemplo de HeadPose de API de rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) aplicação, que controla o atributo HeadPose em tempo real para detetar movimentos principais.