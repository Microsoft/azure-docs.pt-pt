---
title: Migrar do Azure Media Encoder para Media Encoder Standard Microsoft Docs
description: Este tópico discute como migrar do Azure Media Encoder para o processador de media Media Encoder Standard.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 32eab0f729280ad25f45853e05ea982b60a6d9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259341"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Azure Media Encoder para Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo discute os passos para migrar do legado processador de mídia Azure Media Encoder (AME) (que está a ser retirado) para o processador de media Media Encoder Standard. Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md)

Ao codificar ficheiros com AME, os clientes normalmente utilizavam uma cadeia predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p` . Para migrar, o seu código precisa de ser atualizado para utilizar o processador de mídia **Media Encoder Standard** em vez de AME, e uma das [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrar para media Encoder Standard

Aqui está uma amostra de código C# típica que utiliza o processador de mídia legado. 

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

Aqui está a versão atualizada que utiliza o Media Encoder Standard.

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

Se tivesse criado a sua própria predefinição de codificação para a AME utilizando o seu esquema, existe um [esquema equivalente para o Media Encoder Standard](media-services-mes-schema.md). Se tiver dúvidas sobre como mapear as definições mais antigas para o novo codificar, por favor contacte-nos através de mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Diferenças conhecidas 

A Media Encoder Standard é mais robusta, fiável, tem melhor desempenho e produz uma melhor qualidade do que o codificadores AME. Além disso: 

* A Media Encoder Standard produz ficheiros de saída com uma convenção de nomeação diferente da AME.
* A Media Encoder Standard produz artefactos como ficheiros que contêm [metadados](media-services-input-metadata-schema.md) de ficheiros de entrada e [metadados de ficheiros de saída](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
