---
title: Migrar do codificador de mídia do Windows Azure para Media Encoder Standard | Microsoft Docs
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
ms.openlocfilehash: 2e9008d039f014c95d473f3197b48651bdaa5a45
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019371"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrar do codificador de mídia do Windows Azure para Media Encoder Standard

Este artigo discute as etapas para migrar do processador de mídia do Windows Azure Media Encoder (WAME) herdado, que está sendo desativado em 30 de novembro de 2019, para o processador de mídia Media Encoder Standard.

Ao codificar arquivos com WAME, os clientes normalmente usaram uma cadeia de caracteres predefinida nomeada, `H264 Adaptive Bitrate MP4 Set 1080p`como. Para migrar, seu código precisa ser atualizado para usar o processador de mídia **Media Encoder Standard** em vez de WAME, e uma das predefinições equivalentes do [sistema](media-services-mes-presets-overview.md) , como `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrando para o Media Encoder Standard

Aqui está um exemplo C# de código típico que usa o componente herdado. 

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

Se você tiver criado sua própria predefinição de codificação para WAME usando seu esquema, haverá um [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md). Se você tiver dúvidas sobre como mapear as configurações mais antigas para o novo codificador, entre em contato conosco por meio demailto:amshelp@microsoft.com  

## <a name="known-differences"></a>Diferenças conhecidas 

O Media Encoder Standard é mais robusto, confiável, tem um desempenho melhor e produz uma saída de qualidade melhor do que o codificador WAME herdado. Além disso: 

* Media Encoder Standard produz arquivos de saída com uma Convenção de nomenclatura diferente de WAME.
* Media Encoder Standard produz artefatos como arquivos que contêm os [metadados do arquivo de entrada](media-services-input-metadata-schema.md) e os metadados dos arquivos de [saída](media-services-output-metadata-schema.md).
* Conforme documentado na [página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente na seção de perguntas frequentes), ao codificar vídeos usando Media Encoder Standard, você será cobrado com base na duração dos arquivos produzidos como saída. Com o WAME, você será cobrado com base nos tamanhos dos arquivos de vídeo de entrada e nos arquivos de vídeo de saída.

## <a name="next-steps"></a>Passos Seguintes

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
