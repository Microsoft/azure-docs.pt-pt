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
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505809"
---
# <a name="assets-in-azure-media-services"></a>Ativos em Serviços De Mídia Azure

No Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) contém informações sobre ficheiros digitais armazenados no Armazenamento Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados).

Um Ativo é mapeado para um contentor de bolhas na [conta De armazenamento Azure](storage-account-concept.md) e os ficheiros no Ativo são armazenados como bolhas de bloco nesse recipiente. A Media Services suporta os níveis Blob quando a conta utiliza o armazenamento de v2 (GPv2) de propósito geral. Com o GPv2, pode mover ficheiros para [armazenamento Cool ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **O** armazenamento de arquivo é adequado para arquivar ficheiros de origem quando já não é necessário (por exemplo, depois de codificados).

O nível de armazenamento **do Arquivo** só é recomendado para ficheiros de origem muito grandes que já tenham sido codificados e a saída de código de trabalho foi colocada num recipiente de saída blob. As bolhas no recipiente de saída que pretende associar a um Ativo e usar para transmitir ou analisar o seu conteúdo devem existir num nível de armazenamento **Quente** ou **Cool.**

### <a name="naming"></a>Atribuição de nomes 

#### <a name="assets"></a>Elementos

Os nomes do ativo devem ser únicos. Os nomes de recursos v3 da Media Services (por exemplo, Ativos, Empregos, Transformações) estão sujeitos a restrições de nomeação do Gestor de Recursos Azure. Para mais informações, consulte [as convenções de Naming](media-services-apis-overview.md#naming-conventions).

#### <a name="blobs"></a>Blobs

Os nomes dos ficheiros/bolhas dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de blob para um disco NTFS local para processamento.

## <a name="upload-digital-files-into-assets"></a>Faça upload de ficheiros digitais em Ativos

Depois de os ficheiros digitais serem enviados para armazenamento e associados a um Ativo, podem ser utilizados nos Serviços de Media codificação, streaming e análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho comuns dos Serviços de Media é carregar, codificar e transmitir um ficheiro. Esta secção descreve os passos gerais.

> [!TIP]
> Antes de começar a desenvolver, reveja [o Desenvolvimento com Os Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, nomeação de convenções, e assim por diante).

1. Utilize a API dos Serviços de Multimédia v3 para criar um Recurso “de entrada” novo. Esta operação cria um contentor na conta de armazenamento associada à sua conta dos Serviços de Multimédia. A API devolve o nome do recipiente (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se já tiver um recipiente de bolha que pretende associar a um Ativo, pode especificar o nome do recipiente quando criar o Ativo. Atualmente, os Serviços de Multimédia só suportam blobs na raiz do contentor e sem caminhos no nome de ficheiro. Por esse motivo, um contentor com o nome de ficheiro “input.mp4” funcionará. No entanto, um recipiente com o nome de ficheiro "videos/inputs/input.mp4" não funcionará.

    Pode utilizar a CLI do Azure para carregar diretamente para qualquer conta de armazenamento e para qualquer contentor para os quais tenha direitos na sua subscrição.

    O nome do contentor tem de ser exclusivo e seguir as diretrizes de nomenclatura do armazenamento. O nome não tem de seguir a formatação de nomes de contentores de Recursos dos Serviços de Multimédia (Recurso-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenha um URL de SAS com permissões de leitura-escrita que será utilizado para carregar ficheiros digitais para o contentor de Recursos. Pode utilizar a API dos Serviços de Multimédia para [listar os URLs do contentor de recursos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Utilize as APIs ou SDKs de Armazenamento Azure (por exemplo, o [Storage REST API](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para fazer o upload de ficheiros para o recipiente 'Activo'.
4. Utilize as APIs dos Serviços de Multimédia v3 para criar uma Transformação e um Trabalho para processar o Recurso “de entrada”. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos).
5. Transmita o conteúdo do ativo "output".

Para um exemplo completo .NET que mostre como criar o Ativo, obtenha um URL SAS writável para o recipiente do Ativo no armazenamento, e carregue o ficheiro no recipiente armazenado usando o URL SAS, consulte [Criar uma entrada de trabalho a partir de um ficheiro local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades de um Ativo do tipo Datetime estão sempre em formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para um exemplo rest, consulte o Criar um Ativo com exemplo [REST.](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)

O exemplo mostra como criar o **Órgão de Pedido** onde pode especificar descrição, nome do recipiente, conta de armazenamento e outras informações úteis.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Para um exemplo completo, consulte [Criar uma entrada de emprego a partir de um ficheiro local](job-input-from-local-file-how-to.md). Nos Serviços de Media v3, a entrada de um trabalho também pode ser criada a partir de URLs HTTPS (ver [Criar uma entrada de emprego a partir de um URL HTTPS](job-input-from-http-how-to.md)).

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
|[Encriptação do serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportadas|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado|Não suportado|

<sup>1</sup> Enquanto os Serviços de Media suportam o manuseamento de conteúdos na clara/sem qualquer forma de encriptação, não é recomendado fazê-lo.

<sup>2</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus Ativos foram criados com Media Services v2. O que significa que a V3 trabalha com os ativos encriptados de armazenamento existentes, mas não permite a criação de novos.

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Ver [Filtragem, encomenda, paging de entidades dos Serviços de Media.](entities-overview.md)

## <a name="next-steps"></a>Passos Seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
* [Diferenças entre Serviços de Media v2 e v3](migrate-from-v2-to-v3.md)
