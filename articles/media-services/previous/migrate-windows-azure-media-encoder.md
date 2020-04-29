---
title: Migrar do Windows Azure Media Encoder para media Encoder Standard Microsoft Docs
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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76513206"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Windows Azure Media Encoder para media Encoder Standard

Este artigo discute os passos para a migração do legado Processador de Media Windows Azure Media Encoder (WAME) para o processador de media Media Encoder Standard. Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md)

Ao codificar ficheiros com WAME, os clientes normalmente usavam uma cadeia predefinida chamada como `H264 Adaptive Bitrate MP4 Set 1080p`. Para migrar, o seu código precisa de ser atualizado para utilizar o processador **Media Encoder Standard** em `H264 Multiple Bitrate 1080p`vez de WAME, e um dos [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como . 

## <a name="migrating-to-media-encoder-standard"></a>Migração para media Encoder Standard

Aqui está uma amostra típica de código C# que usa o componente legado. 

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

Se criou o seu próprio predefinição de codificação para a WAME utilizando o seu esquema, existe um [esquema equivalente para media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Diferenças conhecidas 

Media Encoder Standard é mais robusto, fiável, tem melhor desempenho, e produz uma melhor produção de qualidade do que o legacy WAME codificador. Além disso: 

* A Media Encoder Standard produz ficheiros de saída com uma convenção de nomeação diferente da WAME.
* Media Encoder Standard produz artefactos como ficheiros que contêm os [metadados](media-services-input-metadata-schema.md) dos ficheiros de entrada e os [metadados dos ficheiros de saída.](media-services-output-metadata-schema.md)
* Tal como documentado na página de [preços](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente na secção DE PERGUNTAS FREQUENTES), quando codifica vídeos utilizando o Media Encoder Standard, é faturado com base na duração dos ficheiros produzidos como saída. Com a WAME, seria faturado com base nos tamanhos dos ficheiros de vídeo de entrada e nos ficheiros de vídeo de saída.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de apoio

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
