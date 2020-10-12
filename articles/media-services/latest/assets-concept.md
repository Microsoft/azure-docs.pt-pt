---
title: Elementos
titleSuffix: Azure Media Services
description: Saiba o que são os ativos e como são usados pela Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 99101f31ec955c822c784180dd8b04bfb8e5f73c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291607"
---
# <a name="assets-in-azure-media-services-v3"></a>Ativos em Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Na Azure Media Services, um [Ativo](/rest/api/media/assets) é um conceito central. É onde você insere meios de comunicação (por exemplo, através de upload ou live ingest), meios de saída (a partir de uma saída de trabalho) e publicação de meios de streaming (para streaming). 

Um Ativo é mapeado para um recipiente blob na [conta de Armazenamento Azure](storage-account-concept.md) e os ficheiros no Ativo são armazenados como bolhas de bloco nesse recipiente. Os ativos contêm informações sobre ficheiros digitais armazenados no Azure Storage (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados).

Os Serviços de Comunicação Social suportam os níveis Blob quando a conta utiliza o armazenamento v2 (GPv2) para fins gerais. Com o GPv2, pode mover ficheiros para [o armazenamento Cool ou Archive](../../storage/blobs/storage-blob-storage-tiers.md). **O** armazenamento de arquivo é adequado para arquivar ficheiros de origem quando já não é necessário (por exemplo, depois de terem sido codificados).

O nível de armazenamento **Archive** só é recomendado para ficheiros de origem muito grandes que já tenham sido codificados e a saída de trabalho codificante foi colocada num recipiente de bolha de saída. As bolhas no recipiente de saída que pretende associar a um Ativo e que utilize para transmitir ou analisar o seu conteúdo devem existir num nível de armazenamento **quente** ou **fresco.**

## <a name="naming"></a>Atribuição de nomes 

### <a name="assets"></a>Elementos

Os nomes do Ativo devem ser únicos. Os nomes de recursos dos Media Services v3 (por exemplo, Ativos, Empregos, Transformações) estão sujeitos a restrições de nomeação do Gestor de Recursos Azure. Para mais informações, consulte [as convenções de nomeação.](media-services-apis-overview.md#naming-conventions)

### <a name="blobs"></a>Blobs

Os nomes de ficheiros/bolhas dentro de um ativo devem seguir tanto os requisitos de [nome blob](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) como os requisitos de [nome NTFS](/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de bolhas para um disco NTFS local para processamento.

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos serviços de mídia](media-services-overview.md)

## <a name="see-also"></a>Consulte também

[Diferenças entre os Serviços de Comunicação Social v2 e v3](migrate-from-v2-to-v3.md)
