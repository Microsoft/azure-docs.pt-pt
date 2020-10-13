---
title: Utilizar o atributo HeadPose
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar o atributo HeadPose para rodar automaticamente o retângulo facial ou detetar gestos na cabeça num feed de vídeo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: fe9cc44542e97880b076d871dde82f5a02a58957
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928977"
---
# <a name="use-the-headpose-attribute"></a>Utilizar o atributo HeadPose

Neste guia, verá como pode utilizar o atributo HeadPose de um rosto detetado para ativar alguns cenários-chave.

## <a name="rotate-the-face-rectangle"></a>Rode o retângulo facial

O retângulo facial, devolvido com cada rosto detetado, marca a localização e o tamanho do rosto na imagem. Por predefinição, o retângulo está sempre alinhado com a imagem (os seus lados são verticais e horizontais); isto pode ser ineficiente para enquadrar rostos angulares. Em situações em que se quer plantar rostos programáticas numa imagem, é melhor ser capaz de rodar o retângulo para a colheita.

A aplicação de amostra [de WPF dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) face usa o atributo HeadPose para rodar os retângulos faciais detetados.

### <a name="explore-the-sample-code"></a>Explore o código de amostra

Pode rodar programáticamente o retângulo facial utilizando o atributo HeadPose. Se especificar este atributo ao detetar rostos (ver [como detetar rostos),](HowtoDetectFacesinImage.md)poderá questioná-lo mais tarde. O seguinte método da aplicação [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) pega numa lista de objetos **DetectedFace** e devolve uma lista de objetos **[Face.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** **Face** aqui é uma classe personalizada que armazena dados de rosto, incluindo as coordenadas de retângulo atualizado. Os novos valores são calculados para **cima,** **à esquerda,** **à largura**e **à altura,** e um novo campo **FaceAngle** especifica a rotação.

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

### <a name="display-the-updated-rectangle"></a>Mostrar o retângulo atualizado

A partir daqui, pode utilizar os objetos **Face** devolvidos no seu visor. As seguintes linhas do [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) mostram como o novo retângulo é renderizado a partir destes dados:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detetar gestos na cabeça

Pode detetar gestos na cabeça como abanar a cabeça e abanar a cabeça rastreando as mudanças do HeadPose em tempo real. Pode utilizar esta funcionalidade como um detetor de vida personalizado.

A deteção de vivação é a tarefa de determinar que um sujeito é uma pessoa real e não uma representação de imagem ou vídeo. Um detetor de gestos de cabeça pode servir como uma forma de ajudar a verificar a vivacidade, especialmente em oposição a uma representação de imagem de uma pessoa.

> [!CAUTION]
> Para detetar gestos de cabeça em tempo real, terá de ligar para a API facial a uma taxa elevada (mais de uma vez por segundo). Se tiver uma subscrição de nível livre (f0), isso não será possível. Se tiver uma subscrição de nível pago, certifique-se de que calculou os custos de escotagem rápida de chamadas de API para deteção de gestos na cabeça.

Consulte a [amostra face HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) no GitHub para obter um exemplo de funcionamento da deteção de gestos na cabeça.

## <a name="next-steps"></a>Passos seguintes

Consulte a aplicação [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) no GitHub para obter um exemplo de trabalho de retângulos faciais rotativos. Ou, consulte a aplicação [Face HeadPose Sample,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) que rastreia o atributo HeadPose em tempo real para detetar movimentos da cabeça.