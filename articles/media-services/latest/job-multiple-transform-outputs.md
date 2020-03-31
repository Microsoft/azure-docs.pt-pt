---
title: Criar um trabalho azure Media Services com múltiplas saídas de transformação
description: Este tópico demonstra como criar um trabalho de Azure Media Services com múltiplas saídas de transformação.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2020
ms.author: juliako
ms.openlocfilehash: dbbeeb33ee46b37ec920fe598483c332d3439689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563144"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Criar um trabalho com várias saídas de transformação

Este tópico mostra como criar uma Transform com duas Saídas Transform. O primeiro exige que a entrada seja codificada para streaming de bitrate adaptativo com um pré-definido [adaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) incorporado. A segunda pede que o sinal de áudio no vídeo de entrada seja processado com o [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets). Após a criação da Transform, pode submeter um trabalho que processe o seu vídeo em conformidade. Uma vez que neste exemplo estamos a especificar duas saídas transformos, temos de especificar duas saídas de trabalho. Pode optar por direcionar ambas as Saídas de Emprego para o mesmo Ativo (como mostrado abaixo), ou pode ter os resultados escritos para separar os Ativos.
 

> [!TIP]
> Antes de começar a desenvolver, reveja [o Desenvolvimento com Os Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, etc.)

## <a name="create-a-transform"></a>Criar uma transformação

O código seguinte mostra como criar uma transformação que produz duas saídas.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>Submeter um emprego

Crie um trabalho com uma entrada DE URL HTTPS e com duas saídas de emprego.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [Códigos de Erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos seguintes

[Amostras v3 do Azure Media Services utilizando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
