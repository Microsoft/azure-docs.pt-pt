---
title: Azure Media Services legacy components | Microsoft Docs
description: This topic discusses Azure Media Services legacy components.
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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: 6a61c868da7b979b1ac5654f4be93b5245179105
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423929"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services legacy components

Over time, there have been steady improvements and enhancements to Media Service components. As a result, some legacy components have been retired. You can find the instructions on how to migrate your application from the legacy component to a current component in the following articles.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Retirement plans of legacy components and migration guidance

We are announcing deprecation of the *Windows Azure Media Encoder* (WAME) and *Azure Media Encoder* (AME) media processors. These processors are being retired on March 31, 2020.

* [Migrate from Windows Azure Media Encoder to Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrate from Azure Media Encoder to Media Encoder Standard](migrate-azure-media-encoder.md)

We are also announcing retirement of the following Media Analytics media processors: 
 
|Nome do processador de mulitmédia|Retirement date|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| January 1 of 2020|This media processor will be replaced by [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). For more information, see [Migrate from Azure Media Indexer 2 to Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|October 1st of 2020|This media processor will be replaced by [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). For more information, see [Migrate from Azure Media Indexer to Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Passos seguintes

[Migration guidance for moving from Media Services v2 to v3](../latest/migrate-from-v2-to-v3.md)
