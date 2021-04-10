---
title: Migrar do Windows Azure Media Encoder para o Media Encoder Standard | Microsoft Docs
description: Este tópico discute como migrar do Windows Azure Media Encoder para o processador de mídia Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a80d25145162c81ef999f8af1015cd590d5a090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011124"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Windows Azure Media Encoder para Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo discute os passos para migrar do legado processador de mídia Windows Azure Media Encoder (WAME) (que está a ser retirado) para o processador de media Media Encoder Standard. Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md)

Ao codificar ficheiros com WAME, os clientes normalmente utilizavam uma cadeia predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p` . Para migrar, o seu código precisa de ser atualizado para utilizar o processador de mídia **Media Encoder Standard** em vez de WAME, e uma das [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrar para media Encoder Standard

Aqui está uma amostra de código C# típica que utiliza o componente legado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
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

Se tivesse criado a sua própria predefinição de codificação para a WAME utilizando o seu esquema, existe um [esquema equivalente para o Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Diferenças conhecidas 

A Media Encoder Standard é mais robusta, fiável, tem melhor desempenho e produz uma melhor qualidade do que o codificader WAME. Além disso: 

* A Media Encoder Standard produz ficheiros de saída com uma convenção de nomeação diferente da WAME.
* A Media Encoder Standard produz artefactos como ficheiros que contêm [metadados](media-services-input-metadata-schema.md) de ficheiros de entrada e [metadados de ficheiros de saída](media-services-output-metadata-schema.md).
* Conforme documentado na [página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente na secção FAQ), quando codifica vídeos usando o Media Encoder Standard, é faturado com base na duração dos ficheiros produzidos como saída. Com a WAME, você seria cobrado com base nos tamanhos dos ficheiros de vídeo de entrada e ficheiros de vídeo de saída.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
