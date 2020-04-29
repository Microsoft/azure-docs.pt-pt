---
title: Migrar do Azure Media Encoder para media Encoder Standard Microsoft Docs
description: Este tema discute como migrar do Azure Media Encoder para o processador media Media Encoder Standard.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76513506"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Encoder Azure Media para media Encoder Standard

Este artigo discute os passos para a migração do legacy Azure Media Encoder (AME) processador de mídia (que está a ser retirado) para o processador Media Encoder Standard. Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md)

Ao codificar ficheiros com AME, os clientes normalmente usavam uma cadeia predefinida chamada como `H264 Adaptive Bitrate MP4 Set 1080p`. Para migrar, o seu código precisa de ser atualizado para utilizar o processador **Media Encoder Standard** em `H264 Multiple Bitrate 1080p`vez de AME, e um dos [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como . 

## <a name="migrating-to-media-encoder-standard"></a>Migração para media Encoder Standard

Aqui está uma amostra típica de código C# que usa o processador de mídia legado. 

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

Se criou o seu próprio predefinição de codificação para a AME utilizando o seu esquema, existe um [esquema equivalente para media Encoder Standard](media-services-mes-schema.md). Se tiver dúvidas sobre como mapear as definições mais antigas para o novo codificador, contacte-nos viamailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Diferenças conhecidas 

A Media Encoder Standard é mais robusta, fiável, tem melhor desempenho e produz uma melhor qualidade de produção do que o legado Codificador AME. Além disso: 

* A Media Encoder Standard produz ficheiros de saída com uma convenção de nomeação diferente da AME.
* Media Encoder Standard produz artefactos como ficheiros que contêm os [metadados](media-services-input-metadata-schema.md) dos ficheiros de entrada e os [metadados dos ficheiros de saída.](media-services-output-metadata-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
