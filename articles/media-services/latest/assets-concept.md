---
title: Ativos nos serviços de multimédia - Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação sobre o que estão ativos, e como elas são usadas pelos serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0fc44bfdb98b81bf218cb2f1824f0f1bb14de4fa
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235674"
---
# <a name="assets"></a>Recursos

Nos serviços de multimédia do Azure, um [Asset](https://docs.microsoft.com/rest/api/media/assets) contém ficheiros digitais (incluindo o vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e ficheiros de legendas) e os metadados relativos a esses ficheiros. Depois dos ficheiros digitais são carregados para um elemento, eles podem ser usados nos serviços de multimédia de codificação, transmissão em fluxo, analisar fluxos de trabalho de conteúdo. Para obter mais informações, consulte a [ficheiros digitais são carregados nos ativos](#upload-digital-files-into-assets) secção abaixo.

Um elemento é mapeado para um contentor de BLOBs no [conta de armazenamento do Azure](storage-account-concept.md) e os ficheiros no elemento são armazenados como blobs de blocos nesse contentor. Serviços de multimédia suportam as camadas de Blob, quando a conta utiliza para fins gerais v2 (GPv2) de armazenamento. Com a GPv2, pode mover arquivos para [acesso esporádico ou arquivo armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Arquivo** armazenamento é adequado para arquivamento de arquivos de origem quando já não for necessário (por exemplo, depois de ter sido codificados).

O **arquivo** camada de armazenamento só é recomendada para ficheiros de origem muito grandes que já tenham sido codificados e o resultado da tarefa de codificação foi colocado num contêiner de blob de saída. Os blobs no contentor de saída que deseja associar um recurso e a utilização para transmitir em fluxo ou analisar os seus conteúdos, tem de existir uma **frequente** ou **esporádico** camada de armazenamento.

> [!NOTE]
> Propriedades de recurso do tipo Datetime são sempre em formato UTC.

## <a name="upload-digital-files-into-assets"></a>Ficheiros digitais são carregados nos ativos

Um dos fluxos de trabalho comuns dos serviços de multimédia é carregar, codificar e transmitir um ficheiro. Esta seção descreve os passos gerais.

> [!TIP]
> Antes de começar a desenvolver, reveja [desenvolver com os serviços de multimédia v3 APIs](media-services-apis-overview.md) (inclui informações sobre como aceder a APIs, as convenções de nomenclatura, etc.)

1. Utilize a API dos Serviços de Multimédia v3 para criar um Recurso “de entrada” novo. Esta operação cria um contentor na conta de armazenamento associada à sua conta dos Serviços de Multimédia. A API devolve o nome do contentor (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Se já tiver um contentor de blobs que queira associar a um Recurso, pode especificar o nome do contentor quando criar o Recurso. Atualmente, os Serviços de Multimédia só suportam blobs na raiz do contentor e sem caminhos no nome de ficheiro. Por esse motivo, um contentor com o nome de ficheiro “input.mp4” funcionará. Contudo, um contentor com o nome de ficheiro "videos/inputs/input.mp4" não funcionará.

    Pode utilizar a CLI do Azure para carregar diretamente para qualquer conta de armazenamento e para qualquer contentor para os quais tenha direitos na sua subscrição. <br/>O nome do contentor tem de ser exclusivo e seguir as diretrizes de nomenclatura do armazenamento. O nome não tem de seguir a formatação de nomes de contentores de Recursos dos Serviços de Multimédia (Recurso-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenha um URL de SAS com permissões de leitura-escrita que será utilizado para carregar ficheiros digitais para o contentor de Recursos. Pode utilizar a API dos Serviços de Multimédia para [listar os URLs do contentor de recursos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Utilize as APIs ou os SDKs do Armazenamento do Azure (por exemplo, a [API REST de Armazenamento](../../storage/common/storage-rest-api-auth.md), o [Java SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) ou o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar ficheiros para o contentor de Recursos. 
4. Utilize as APIs dos Serviços de Multimédia v3 para criar uma Transformação e um Trabalho para processar o Recurso “de entrada”. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos).
5. Stream o conteúdo do elemento de "saída".

Para obter um exemplo de .NET completo que mostra como: criar o elemento, obter um URL de SAS gravável para contentor do recurso no armazenamento, carregue o ficheiro para o contentor no armazenamento com o URL de SAS, consulte [criar uma entrada da tarefa a partir de um ficheiro local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo elemento

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo do REST, veja a [criar um elemento com REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) exemplo.

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

Para obter um exemplo completo, veja [criar uma entrada da tarefa a partir de um ficheiro local](job-input-from-local-file-how-to.md). Em serviços de multimédia v3, entrada de um trabalho também pode ser criada a partir de URLs de HTTPS (veja [criar uma entrada da tarefa a partir de um URL HTTPS](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus ativos inativos, os recursos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona nos serviços de multimédia:

|Opção de encriptação|Descrição|Serviços de Multimédia v2|Serviços de Multimédia v3|
|---|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia|Encriptação AES-256, chave gerida pelos serviços de multimédia|Suportado<sup>(1)</sup>|Não suportado<sup>(2)</sup>|
|[Encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chave de encriptação do lado do servidor oferecidas pelo armazenamento do Azure, gerida pelo Azure ou pelo cliente|Suportadas|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferecidas pelo armazenamento do Azure, chave gerida pelo cliente no Cofre de chaves de encriptação do lado do cliente|Não suportado|Não suportado|

<sup>1</sup> enquanto os serviços de multimédia oferece suporte à manipulação de conteúdo de forma/sem qualquer outra forma de encriptação, ao fazê-lo por isso, não é recomendado.

<sup>2</sup> em serviços de multimédia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para em versões anteriores compatibilidade quando os recursos foram criados com os serviços de multimédia v2. O que significa v3 funciona com o armazenamento existente encriptado ativos, mas não permitirá que a criação de novos itens.

## <a name="next-steps"></a>Passos Seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
* [Diferenças entre o suporte de dados de serviços v2 e v3](migrate-from-v2-to-v3.md)
