---
title: Subclip um vídeo ao codificar com os Media Services
description: Este tópico descreve como subclip um vídeo ao codificar com a Azure Media Services usando .NET SDK
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b70d08363db212e5039690ceaaef5dbb55bcd65f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107278"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Subclip um vídeo ao codificar com os Media Services - .NET

Pode aparar ou subclipar um vídeo ao codificar o vídeo usando um [Trabalho](/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [Transform](/rest/api/media/transforms) que seja construído utilizando as predefinições Predefinidas [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](/rest/api/media/transforms/createorupdate#standardencoderpreset)

O exemplo C# a seguir cria um trabalho que apara um vídeo num Ativo à medida que apresenta um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- [Criar uma conta Azure Media Services](./account-create-how-to.md)
- Criar uma Transformação e uma entrada e saída Ativos. Pode ver como criar um Transform e inserir e produzir Ativos no [Upload, codificar e transmitir vídeos usando o tutorial .NET.](stream-files-tutorial-with-api.md)
- Reveja o tópico do [conceito de codificação.](encode-concept.md)

## <a name="example"></a>Exemplo

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Passos seguintes

[Como codificar com uma transformação personalizada](encode-custom-presets-how-to.md) 
