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
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68bb1fea88ab7ba30e0ba07839f2d962840b7818
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921186"
---
# <a name="assets-in-azure-media-services"></a>Ativos em Serviços De Mídia Azure

Na Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito central. É onde você insere os meios de comunicação (por exemplo, através de upload ou ingestão ao vivo), meios de saída (a partir de uma saída de trabalho) e publica meios de (para streaming). 

Um Ativo é mapeado para um contentor de bolhas na [conta De armazenamento Azure](storage-account-concept.md) e os ficheiros no Ativo são armazenados como bolhas de bloco nesse recipiente. Os ativos contêm informações sobre ficheiros digitais armazenados no Armazenamento Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados).

A Media Services suporta os níveis Blob quando a conta utiliza o armazenamento de v2 (GPv2) de propósito geral. Com o GPv2, pode mover ficheiros para [armazenamento Cool ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **O** armazenamento de arquivo é adequado para arquivar ficheiros de origem quando já não é necessário (por exemplo, depois de codificados).

O nível de armazenamento **do Arquivo** só é recomendado para ficheiros de origem muito grandes que já tenham sido codificados e a saída de código de trabalho foi colocada num recipiente de saída blob. As bolhas no recipiente de saída que pretende associar a um Ativo e usar para transmitir ou analisar o seu conteúdo devem existir num nível de armazenamento **Quente** ou **Cool.**

## <a name="naming"></a>Atribuição de nomes 

### <a name="assets"></a>Elementos

Os nomes do ativo devem ser únicos. Os nomes de recursos v3 da Media Services (por exemplo, Ativos, Empregos, Transformações) estão sujeitos a restrições de nomeação do Gestor de Recursos Azure. Para mais informações, consulte [as convenções de Naming](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobs

Os nomes dos ficheiros/bolhas dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de blob para um disco NTFS local para processamento.

## <a name="map-v3-asset-properties-to-v2"></a>Mapa v3 propriedades de ativos para v2

A tabela seguinte mostra como as propriedades do [Ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)no mapa v3 para as propriedades do Ativo em v2.

|propriedades v3|propriedades v2|
|---|---|
|`id` - (único) o caminho completo do Gestor de Recursos Azure, veja exemplos em [Ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` - (único) ver convenções de [nomeação](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` - valor (único) começa com o prefixo `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opções de criação)|
|`type`||

## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus ativos inativos, os recursos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona nos serviços de multimédia:

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia|Encriptação AES-256, chave gerida pela Media Services.|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportado|Suportado|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado|Não suportado|

<sup>1</sup> Enquanto os Serviços de Media suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendado fazê-lo.

<sup>2</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Media Services v2. O que significa que a V3 trabalha com os ativos encriptados de armazenamento existentes, mas não permite a criação de novos.

## <a name="next-steps"></a>Passos seguintes

[Gerir ativos em Serviços de Media](manage-asset-concept.md)

## <a name="also-see"></a>Também ver

[Diferenças entre Serviços de Media v2 e v3](migrate-from-v2-to-v3.md)
