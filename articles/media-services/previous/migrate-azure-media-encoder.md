---
title: Migrar do Azure Media Encoder para Media Encoder Standard | Microsoft Docs
description: Este tópico discute como migrar do codificador de mídia do Azure para o processador de mídia Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513506"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Azure Media Encoder para Media Encoder Standard

Este artigo discute as etapas para migrar do processador de mídia do Azure Media Encoder (AME) herdado (que está sendo desativado) para o processador de mídia Media Encoder Standard. Para as datas de desativação, consulte este tópico de [componentes herdados](legacy-components.md) .

Ao codificar arquivos com o AME, os clientes normalmente usaram uma cadeia de caracteres predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p`. Para migrar, seu código precisa ser atualizado para usar o processador de mídia **Media Encoder Standard** em vez de ame, e uma das [predefinições de sistema](media-services-mes-presets-overview.md) equivalentes, como `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrando para o Media Encoder Standard

Aqui está um exemplo C# de código típico que usa o processador de mídia herdado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Aqui está a versão atualizada que usa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Cenários avançados 

Se você tiver criado sua própria predefinição de codificação para AME usando seu esquema, haverá um [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md). Se você tiver dúvidas sobre como mapear as configurações mais antigas para o novo codificador, entre em contato conosco por meio de mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Diferenças conhecidas 

Media Encoder Standard é mais robusto, confiável, tem melhor desempenho e produz uma saída de qualidade melhor do que o codificador do AME herdado. Além disso: 

* Media Encoder Standard produz arquivos de saída com uma Convenção de nomenclatura diferente da AME.
* Media Encoder Standard produz artefatos como arquivos que contêm os [metadados do arquivo de entrada](media-services-input-metadata-schema.md) e os metadados dos arquivos de [saída](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Passos seguintes

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
