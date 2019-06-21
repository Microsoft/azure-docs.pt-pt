---
title: Um vídeo do subclip ao codificar com os serviços de multimédia do Azure
description: Este tópico descreve como um vídeo do subclip ao codificar com os serviços de multimédia do Azure com o .NET SDK
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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305218"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Um vídeo do subclip quando encoding com Media Services - .NET

Pode cortar ou um vídeo do subclip ao codificar usando um [tarefa](https://docs.microsoft.com/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [transformar](https://docs.microsoft.com/rest/api/media/transforms) criada através de um a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefinições, ou o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) configurações predefinidas.

O seguinte C# exemplo cria uma tarefa que corta um vídeo de um recurso como submete um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tópico, tem de:

- [Criar uma conta de Media Services do Azure](create-account-cli-how-to.md)
- Criar uma transformação e de entrada e saída ativos. Pode ver como criar uma transformação e recursos de entrada e saídos na [carregar, codificar e transmitir vídeos com o .NET](stream-files-tutorial-with-api.md) tutorial.
- Reveja os [conceito de codificação](encoding-concept.md) tópico.

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

## <a name="next-steps"></a>Passos Seguintes

[Como codificar com uma transformação personalizados](customize-encoder-presets-how-to.md) 
