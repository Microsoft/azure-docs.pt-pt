---
title: Ativos nos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que estão ativos, e como elas são usadas pelos serviços de multimédia do Azure.
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
ms.openlocfilehash: 3dc1866a3c0339bca0c27fb53894a14581e88490
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390504"
---
# <a name="assets"></a>Elementos

Nos serviços de mídia do Azure, um [ativo](https://docs.microsoft.com/rest/api/media/assets) contém informações sobre arquivos digitais armazenados no armazenamento do Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada). 

Um ativo é mapeado para um contêiner de blob na [conta de armazenamento do Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como BLOBs de blocos nesse contêiner. Os serviços de mídia dão suporte a camadas de blob quando a conta usa o armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento frio ou de arquivo morto](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento de **arquivo** é adequado para arquivar arquivos de origem quando não for mais necessário (por exemplo, depois que eles tiverem sido codificados).

A camada de armazenamento de **arquivo** é recomendada somente para arquivos de origem muito grandes que já foram codificados e a saída do trabalho de codificação foi colocada em um contêiner de blob de saída. Os BLOBs no contêiner de saída que você deseja associar a um ativo e usam para transmitir ou analisar seu conteúdo devem existir em uma camada de armazenamento **quente** ou **fria** .

### <a name="naming-blobs"></a>Nomeando BLOBs

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome do blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de nome do [NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de BLOBs para um disco NTFS local para processamento.

## <a name="upload-digital-files-into-assets"></a>Carregar arquivos digitais em ativos

Depois que os arquivos digitais são carregados no armazenamento e associados a um ativo, eles podem ser usados na codificação de serviços de mídia, no streaming, na análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho comuns dos serviços de mídia é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

> [!TIP]
> Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura, etc.)

1. Utilize a API dos Serviços de Multimédia v3 para criar um Recurso “de entrada” novo. Esta operação cria um contentor na conta de armazenamento associada à sua conta dos Serviços de Multimédia. A API retorna o nome do contêiner (por exemplo `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`,).
   
    Se já tiver um contentor de blobs que queira associar a um Recurso, pode especificar o nome do contentor quando criar o Recurso. Atualmente, os Serviços de Multimédia só suportam blobs na raiz do contentor e sem caminhos no nome de ficheiro. Por esse motivo, um contentor com o nome de ficheiro “input.mp4” funcionará. Contudo, um contentor com o nome de ficheiro "videos/inputs/input.mp4" não funcionará.

    Pode utilizar a CLI do Azure para carregar diretamente para qualquer conta de armazenamento e para qualquer contentor para os quais tenha direitos na sua subscrição. <br/>O nome do contentor tem de ser exclusivo e seguir as diretrizes de nomenclatura do armazenamento. O nome não tem de seguir a formatação de nomes de contentores de Recursos dos Serviços de Multimédia (Recurso-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenha um URL de SAS com permissões de leitura-escrita que será utilizado para carregar ficheiros digitais para o contentor de Recursos. Pode utilizar a API dos Serviços de Multimédia para [listar os URLs do contentor de recursos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Use as APIs de armazenamento do Azure ou SDKs (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar arquivos no contêiner de ativos. 
4. Utilize as APIs dos Serviços de Multimédia v3 para criar uma Transformação e um Trabalho para processar o Recurso “de entrada”. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos).
5. Transmita o conteúdo do ativo de "saída".

Para obter um exemplo .NET completo que mostra como: criar o ativo, obtenha uma URL SAS gravável para o contêiner do ativo no armazenamento, carregue o arquivo no contêiner no armazenamento usando a URL SAS, consulte [criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades do ativo do tipo DateTime estão sempre no formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo de REST, consulte o exemplo [criar um ativo com REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) .

O exemplo mostra como criar o **Corpo do Pedido**, onde pode especificar informações úteis, como uma descrição, o nome do contentor, a conta de armazenamento, entre outras.

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

Para obter um exemplo completo, consulte [criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md). No Media Services V3, a entrada de um trabalho também pode ser criada a partir de URLs HTTPS (consulte [criar uma entrada de trabalho de uma URL https](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mapear Propriedades de ativos V3 para v2

A tabela a seguir mostra como as propriedades do [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)em v3 são mapeadas para as propriedades do ativo na v2.

|Propriedades v3|Propriedades de v2|
|---|---|
|ID-(exclusivo) o caminho de Azure Resource Manager completo, consulte os exemplos no [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|nome-(exclusivo) consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|ID-o valor (exclusivo) começa com `nb:cid:UUID:` o prefixo.|
|criado|Criado|
|description|Name|
|lastModified|lastModified|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| Opções (opções de criação)|
|type||

## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus ativos inativos, os recursos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona nos serviços de multimédia:

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia|Encriptação AES-256, chave gerida pelos serviços de multimédia|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chave de encriptação do lado do servidor oferecidas pelo armazenamento do Azure, gerida pelo Azure ou pelo cliente|Suportadas|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferecidas pelo armazenamento do Azure, chave gerida pelo cliente no Cofre de chaves de encriptação do lado do cliente|Não suportado|Não suportado|

<sup>1</sup> enquanto os serviços de multimédia oferece suporte à manipulação de conteúdo de forma/sem qualquer outra forma de encriptação, ao fazê-lo por isso, não é recomendado.

<sup>2</sup> em serviços de multimédia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para em versões anteriores compatibilidade quando os recursos foram criados com os serviços de multimédia v2. O que significa v3 funciona com o armazenamento existente encriptado ativos, mas não permitirá que a criação de novos itens.

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Consulte [filtragem, ordenação, paginação de entidades de serviços de mídia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
* [Diferenças entre os serviços de mídia V2 e v3](migrate-from-v2-to-v3.md)
