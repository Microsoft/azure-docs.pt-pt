---
title: Gerir ativos em Azure Media Services
titleSuffix: Azure Media Services
description: Um ativo onde insere meios de comunicação (por exemplo, através de upload ou ingestão ao vivo), meios de saída (a partir de uma saída de trabalho) e publicação de meios de comunicação (para streaming). Este tópico dá uma visão geral de como criar um novo ativo e carregar ficheiros.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6accd303ba11c4c1406c7a157fa8176972fc7a3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022912"
---
# <a name="manage-assets"></a>Gerir recursos

Na Azure Media Services, um [Ativo](/rest/api/media/assets) é onde você 

* enviar ficheiros de mídia para um ativo,
* ingerir e arquivar fluxos ao vivo em um ativo,
* produção dos resultados de um trabalho de codificação de trabalho de análise a um ativo,
* publicar meios de streaming, 
* descarregar ficheiros de um ativo.

Este tópico dá uma visão geral de como carregar ficheiros num ativo e realizar outras operações comuns. Também fornece ligações a amostras de código e tópicos relacionados.

## <a name="prerequisite"></a>Pré-requisito 

Antes de começar a desenvolver-se, reveja:

* [Conceitos](concepts-overview.md)
* [Desenvolvimento com Serviços de Mídia v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, e assim por diante) 

## <a name="upload-media-files-into-an-asset"></a>Faça upload de ficheiros de mídia para um ativo

Depois de os ficheiros digitais serem enviados para armazenamento e associados a um Ativo, podem ser utilizados nos Serviços de Comunicação Social codificando, streaming e analisando fluxos de trabalho de conteúdo. Um dos fluxos de trabalho comuns dos Serviços de Comunicação é carregar, codificar e transmitir um ficheiro. Esta secção descreve os passos gerais.

1. Utilize a API dos Serviços de Multimédia v3 para criar um Recurso “de entrada” novo. Esta operação cria um contentor na conta de armazenamento associada à sua conta dos Serviços de Multimédia. A API devolve o nome do recipiente (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"` ).

    Se já tiver um recipiente blob que pretende associar a um Ativo, pode especificar o nome do recipiente quando criar o Ativo. Atualmente, os Serviços de Multimédia só suportam blobs na raiz do contentor e sem caminhos no nome de ficheiro. Por esse motivo, um contentor com o nome de ficheiro “input.mp4” funcionará. No entanto, um recipiente com o nome do ficheiro "vídeos/entradas/input.mp4" não funciona.

    Pode utilizar a CLI do Azure para carregar diretamente para qualquer conta de armazenamento e para qualquer contentor para os quais tenha direitos na sua subscrição.

    O nome do contentor tem de ser exclusivo e seguir as diretrizes de nomenclatura do armazenamento. O nome não tem de seguir a formatação de nomes de contentores de Recursos dos Serviços de Multimédia (Recurso-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenha um URL de SAS com permissões de leitura-escrita que será utilizado para carregar ficheiros digitais para o contentor de Recursos.

    Pode utilizar a API dos Serviços de Multimédia para [listar os URLs do contentor de recursos](/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** leva um parâmetro [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) no qual definiu `expiryTime` . O tempo deve ser definido para < 24 horas.

    [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) devolve vários URLs SAS, uma vez que existem duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque ajuda com a falha e a rotação perfeita das chaves da conta de armazenamento. O primeiro URL SAS representa a primeira chave de conta de armazenamento e o segundo URL SAS representa a segunda chave.
3. Utilize as APIs ou SDKs de Armazenamento Azure (por exemplo, a [API de Armazenamento REST](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para enviar ficheiros para o contentor Ativo.
4. Utilize as APIs dos Serviços de Multimédia v3 para criar uma Transformação e um Trabalho para processar o Recurso “de entrada”. Para obter mais informações, veja [Transforms and Jobs](./transforms-jobs-concept.md) (Transformações e Trabalhos).
5. Transmita o conteúdo do ativo "output".

### <a name="create-a-new-asset"></a>Criar um recurso novo

> [!NOTE]
> As propriedades de um Ativo do tipo Datatime estão sempre em formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para um exemplo REST, consulte o [Exemplo Criar um Ativo com REST.](/rest/api/media/assets/createorupdate#examples)

O exemplo mostra como criar o **Corpo de Pedidos** onde pode especificar descrição, nome do recipiente, conta de armazenamento e outras informações úteis.

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

### <a name="see-also"></a>Ver também

* [Criar uma entrada de trabalho a partir de um arquivo local](job-input-from-local-file-how-to.md)
* [Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingerir e arquivar fluxos ao vivo em um ativo

Nos Media Services, um objeto [live output](/rest/api/media/liveoutputs) é como um gravador de vídeo digital que irá capturar e gravar o seu live stream num ativo na sua conta de Media Services. O conteúdo registado é persistido no recipiente definido pelo recurso [Ativo.](/rest/api/media/assets)

Para obter mais informações, veja:

* [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
* [Streaming tutorial ao vivo](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Produza os resultados de um trabalho para um ativo

Nos Serviços de Comunicação Social, ao processar os seus vídeos (por exemplo, codificar ou analisar) é necessário criar um [ativo](assets-concept.md) de saída para armazenar o resultado do seu [trabalho](transforms-jobs-concept.md).

Para obter mais informações, veja:

* [Codificar um vídeo](encoding-concept.md)
* [Criar uma entrada de trabalho a partir de um arquivo local](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publicar um ativo para streaming

Para publicar um ativo para streaming, é necessário criar um [localizador de streaming.](streaming-locators-concept.md) O localizador de streaming precisa de saber o nome do ativo que pretende publicar. 

Para obter mais informações, veja:

[Tutorial: Carregar, codificar e transmitir vídeos com o Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Baixar resultados de um trabalho a partir de um ativo de saída

Em seguida, pode transferir estes resultados do seu trabalho para uma pasta local utilizando APIs de Media Service e Armazenamento. 

Veja o exemplo dos [ficheiros de descarregamento.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtragem, encomenda, paging

Ver [Filtragem, encomenda, paging de entidades de Serviços de Comunicação](entities-overview.md)Social.

## <a name="next-steps"></a>Passos seguintes

Veja os exemplos completos de código que demonstram como carregar, codificar, analisar, transmitir ao vivo e a pedido: 

* [Java,](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) 
* [.NET,](/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/) 
* [DESCANSE.](/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/)
