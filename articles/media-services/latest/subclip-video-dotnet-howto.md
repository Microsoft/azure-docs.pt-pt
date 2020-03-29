---
title: Subclipe um vídeo ao codificar com a Azure Media Services
description: Este tópico descreve como subclipe um vídeo ao codificar com a Azure Media Services usando .NET SDK
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305218"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Subclipe um vídeo ao codificar com os Media Services - .NET

Pode cortar ou subclipe um vídeo ao codificar-o com um [Job](https://docs.microsoft.com/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construído utilizando os predefinições [BuiltInStandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou os predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

O exemplo c# seguinte cria um trabalho que apara um vídeo num Ativo ao submeter um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- [Criar uma conta Azure Media Services](create-account-cli-how-to.md)
- Criar uma Transformação e um Ativo de entrada e saída. Pode ver como criar um Transform e entrada e output Assets no [Upload, codificar e transmitir vídeos usando o tutorial .NET.](stream-files-tutorial-with-api.md)
- Reveja o tópico do [conceito de codificação.](encoding-concept.md)

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

[Como codificar com uma transformação personalizada](customize-encoder-presets-how-to.md) 
