---
title: Elementos
titleSuffix: Azure Media Services
description: Saiba quais são os ativos e como são usados pela Azure Media Services.
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
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087905"
---
# <a name="assets-in-azure-media-services-v3"></a>Ativos em Azure Media Services v3

Na Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito central. É onde você insere os meios de comunicação (por exemplo, através de upload ou ingestão ao vivo), meios de saída (a partir de uma saída de trabalho) e publica meios de (para streaming). 

Um Ativo é mapeado para um contentor de bolhas na [conta De armazenamento Azure](storage-account-concept.md) e os ficheiros no Ativo são armazenados como bolhas de bloco nesse recipiente. Os ativos contêm informações sobre ficheiros digitais armazenados no Armazenamento Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados).

A Media Services suporta os níveis Blob quando a conta utiliza o armazenamento de v2 (GPv2) de propósito geral. Com o GPv2, pode mover ficheiros para [armazenamento Cool ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **O** armazenamento de arquivo é adequado para arquivar ficheiros de origem quando já não é necessário (por exemplo, depois de codificados).

O nível de armazenamento **do Arquivo** só é recomendado para ficheiros de origem muito grandes que já tenham sido codificados e a saída de código de trabalho foi colocada num recipiente de saída blob. As bolhas no recipiente de saída que pretende associar a um Ativo e usar para transmitir ou analisar o seu conteúdo devem existir num nível de armazenamento **Quente** ou **Cool.**

## <a name="naming"></a>Atribuição de nomes 

### <a name="assets"></a>Elementos

Os nomes do ativo devem ser únicos. Os nomes de recursos v3 da Media Services (por exemplo, Ativos, Empregos, Transformações) estão sujeitos a restrições de nomeação do Gestor de Recursos Azure. Para mais informações, consulte [as convenções de Naming](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobs

Os nomes dos ficheiros/bolhas dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de blob para um disco NTFS local para processamento.

## <a name="next-steps"></a>Passos seguintes

[Gerir ativos em Serviços de Media](manage-asset-concept.md)

## <a name="see-also"></a>Consulte também

[Diferenças entre Serviços de Media v2 e v3](migrate-from-v2-to-v3.md)
