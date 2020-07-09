---
title: Elementos
titleSuffix: Azure Media Services
description: Saiba o que são os ativos e como são usados pela Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79087905"
---
# <a name="assets-in-azure-media-services-v3"></a>Ativos em Azure Media Services v3

Na Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito central. É onde você insere meios de comunicação (por exemplo, através de upload ou live ingest), meios de saída (a partir de uma saída de trabalho) e publicação de meios de streaming (para streaming). 

Um Ativo é mapeado para um recipiente blob na [conta de Armazenamento Azure](storage-account-concept.md) e os ficheiros no Ativo são armazenados como bolhas de bloco nesse recipiente. Os ativos contêm informações sobre ficheiros digitais armazenados no Azure Storage (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados).

Os Serviços de Comunicação Social suportam os níveis Blob quando a conta utiliza o armazenamento v2 (GPv2) para fins gerais. Com o GPv2, pode mover ficheiros para [o armazenamento Cool ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **O** armazenamento de arquivo é adequado para arquivar ficheiros de origem quando já não é necessário (por exemplo, depois de terem sido codificados).

O nível de armazenamento **Archive** só é recomendado para ficheiros de origem muito grandes que já tenham sido codificados e a saída de trabalho codificante foi colocada num recipiente de bolha de saída. As bolhas no recipiente de saída que pretende associar a um Ativo e que utilize para transmitir ou analisar o seu conteúdo devem existir num nível de armazenamento **quente** ou **fresco.**

## <a name="naming"></a>Atribuição de nomes 

### <a name="assets"></a>Elementos

Os nomes do Ativo devem ser únicos. Os nomes de recursos dos Media Services v3 (por exemplo, Ativos, Empregos, Transformações) estão sujeitos a restrições de nomeação do Gestor de Recursos Azure. Para mais informações, consulte [as convenções de nomeação.](media-services-apis-overview.md#naming-conventions)

### <a name="blobs"></a>Blobs

Os nomes de ficheiros/bolhas dentro de um ativo devem seguir tanto os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) como os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de bolhas para um disco NTFS local para processamento.

## <a name="next-steps"></a>Próximos passos

[Gerir ativos em Serviços de Media](manage-asset-concept.md)

## <a name="see-also"></a>Veja também

[Diferenças entre os Serviços de Comunicação Social v2 e v3](migrate-from-v2-to-v3.md)
