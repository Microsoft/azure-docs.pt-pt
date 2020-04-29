---
title: Gerir ativos em Azure Media Services
titleSuffix: Azure Media Services
description: Um ativo onde insere os meios de comunicação (por exemplo, através de upload ou ingestão ao vivo), meios de saída (a partir de uma saída de trabalho) e publique meios de (para streaming). Este tópico dá uma visão geral de como criar um novo ativo e carregar ficheiros.
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
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582204"
---
# <a name="manage-assets"></a>Gerir recursos

Na Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) é onde 

* carregar ficheiros de mídia para um ativo,
* ingerir e arquivar correntes ao vivo em um ativo,
* produção dos resultados de um trabalho de analítica para um ativo,
* publicar meios de comunicação para streaming, 
* descarregar ficheiros de um ativo.

Este tópico dá uma visão geral de como enviar ficheiros para um ativo e realizar outras operações comuns. Também fornece links para amostras de código e tópicos relacionados.

## <a name="prerequisite"></a>Pré-requisito 

Antes de começar a desenvolver, reveja:

* [Conceitos](concepts-overview.md)
* [Desenvolvimento com Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, nomeação de convenções, e assim por diante) 

## <a name="upload-media-files-into-an-asset"></a>Faça upload de ficheiros de mídia para um ativo

Depois de os ficheiros digitais serem enviados para armazenamento e associados a um Ativo, podem ser utilizados nos Serviços de Media codificação, streaming e análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho comuns dos Serviços de Media é carregar, codificar e transmitir um ficheiro. Esta secção descreve os passos gerais.

1. Utilize a API dos Serviços de Multimédia v3 para criar um Recurso “de entrada” novo. Esta operação cria um contentor na conta de armazenamento associada à sua conta dos Serviços de Multimédia. A API devolve o nome `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`do recipiente (por exemplo, ).

    Se já tiver um recipiente de bolha que pretende associar a um Ativo, pode especificar o nome do recipiente quando criar o Ativo. Atualmente, os Serviços de Multimédia só suportam blobs na raiz do contentor e sem caminhos no nome de ficheiro. Por esse motivo, um contentor com o nome de ficheiro “input.mp4” funcionará. No entanto, um recipiente com o nome de ficheiro "videos/inputs/input.mp4" não funcionará.

    Pode utilizar a CLI do Azure para carregar diretamente para qualquer conta de armazenamento e para qualquer contentor para os quais tenha direitos na sua subscrição.

    O nome do contentor tem de ser exclusivo e seguir as diretrizes de nomenclatura do armazenamento. O nome não tem de seguir a formatação de nomes de contentores de Recursos dos Serviços de Multimédia (Recurso-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenha um URL de SAS com permissões de leitura-escrita que será utilizado para carregar ficheiros digitais para o contentor de Recursos.

    Pode utilizar a API dos Serviços de Multimédia para [listar os URLs do contentor de recursos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** tem um parâmetro [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) `expiryTime`no qual se define . A hora deve ser fixada para < 24 horas.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) devolve vários URLs SAS, uma vez que existem duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque ajuda com a falha e rotação perfeita das chaves da conta de armazenamento. O primeiro URL SAS representa a primeira chave da conta de armazenamento e o segundo URL SAS representa a segunda chave.
3. Utilize as APIs ou SDKs de Armazenamento Azure (por exemplo, o [Storage REST API](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para fazer o upload de ficheiros para o recipiente 'Activo'.
4. Utilize as APIs dos Serviços de Multimédia v3 para criar uma Transformação e um Trabalho para processar o Recurso “de entrada”. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos).
5. Transmita o conteúdo do ativo "output".

### <a name="create-a-new-asset"></a>Criar um recurso novo

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

### <a name="see-also"></a>Consulte também

* [Criar uma entrada de emprego a partir de um arquivo local](job-input-from-local-file-how-to.md)
* [Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingest e arquivar fluxos ao vivo em um ativo

Nos Serviços de Media, um objeto [de Saída Ao Vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) é como um gravador de vídeo digital que vai capturar e gravar o seu live stream em um ativo na sua conta de Media Services. O conteúdo registado é permanecido no recipiente definido pelo recurso [Ativo.](https://docs.microsoft.com/rest/api/media/assets)

Para obter mais informações, consulte:

* [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
* [Tutorial ao vivo de streaming](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Produção dos resultados de um trabalho para um ativo

Nos Serviços de Media, ao processar os seus vídeos (por exemplo, codificar ou analisar) é necessário criar um [ativo](assets-concept.md) de saída para armazenar o resultado do seu [trabalho](transforms-jobs-concept.md).

Para obter mais informações, consulte:

* [Codificar um vídeo](encoding-concept.md)
* [Criar uma entrada de emprego a partir de um arquivo local](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publicar um ativo para streaming

Para publicar um ativo para streaming, precisa de criar um Localizador de [Streaming.](streaming-locators-concept.md) O localizador de streaming precisa de saber o nome do ativo que pretende publicar. 

Para obter mais informações, consulte:

[Tutorial: Upload, codificação e streaming de vídeos com Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Descarregue os resultados de um trabalho a partir de um ativo de saída

Em seguida, pode transferir estes resultados do seu trabalho para uma pasta local usando Media Service e APIs de Armazenamento. 

Consulte o exemplo dos [ficheiros de descarregamento.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtragem, encomenda, paging

Ver [Filtragem, encomenda, paging de entidades dos Serviços de Media.](entities-overview.md)

## <a name="next-steps"></a>Passos seguintes

Consulte os exemplos completos de código que demonstram como carregar, codificar, analisar, transmitir ao vivo e a pedido: 

* [Java,](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) 
* [.net,](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/) 
* [DESCANSE.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/)
